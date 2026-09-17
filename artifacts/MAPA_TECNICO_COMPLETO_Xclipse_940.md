# Mapa técnico completo da Samsung Xclipse 940

**Projeto interno:** XO940
**Alvo:** Xclipse 940 no Samsung SM-S721B / plataforma s5e9945 / r12s
**Base:** Device Tree, fonte Samsung inventariada, logs de runtime, traces P2/P3/P4/P5 e inventários ELF fornecidos pelo usuário
**Status:** mapa de estudo; não é especificação completa nem prova de driver independente

## 1. Visão geral

```text
SoC Exynos / Android
└── Xclipse 940 / bloco G3D
    ├── Device Tree e plataforma
    ├── power-domain, clocks, DVFS, thermal, reset e AFM/IFPO
    ├── SGPU kernel driver / DRM
    │   ├── UAPI SGPU
    │   ├── GEM/BO/TTM/DMA-BUF
    │   ├── VM/PTE/PDE/IOMMU
    │   ├── scheduler
    │   ├── GFX / COMPUTE / SDMA
    │   ├── rings / IBs / doorbells
    │   ├── fences / syncobjs / dependências
    │   └── firmware / recovery
    ├── render node SGPU: /dev/dri/renderD128
    ├── Android vendor
    │   ├── libdrm_sgpu.so
    │   ├── vulkan.samsung.so
    │   ├── libOpenCL.so
    │   ├── libSGPUOpenCL.so
    │   ├── SurfaceFlinger / RenderThread / Gralloc
    │   └── linker namespaces / SELinux / HAL
    └── possíveis clientes independentes
        ├── libdrm/client SGPU mínimo
        ├── backend compute experimental
        └── backend Vulkan/Mesa experimental
```

## 2. Device Tree e plataforma

O nó observado é `/sys/firmware/devicetree/base/sgpu@22200000`, com compatible `samsung-sgpu,samsung-sgpu`. Os filhos observados incluem `gpu_pm`, `gpu_doorbell`, `gpu_debug`, `gpu_smntarg` e `gpu_sysreg`. As regiões nomeadas são `gpu`, `doorbell`, `debug`, `pwrctl`, `sysreg` e `htu`.

As interrupções observadas são `SGPU` e `GPU-AFM`. O nó referencia o domínio `pd_g3dcore@0`/`pd_g3dcore` pelo phandle `0xc3`. Também aparecem frequência/DVFS, IFPO, thermal G3D, memória reservada `sgpu_rmem`, `gpu_buffer_dma_heap`, grupos SysMMU/IOMMU e BTS G3D.

**Leitura para um driver:** a Device Tree fornece recursos e relações de plataforma; ela não fornece sozinha o formato dos comandos, a ABI do usuário, a ISA ou a semântica de todos os registradores.

## 3. Identidade observada

| Campo | Valor observado | Confiança |
| --- | --- | --- |
| Dispositivo | SM-S721B / r12s | confirmada nos logs |
| Plataforma | erd9945 / s5e9945 | confirmada nos logs e fonte |
| GPU | Xclipse 940 / família MGFX 147 | confirmada na captura |
| Device ID | `0x000073a0` | confirmada na captura |
| Chip revision | `0x02600200` | confirmada na captura |
| GFX IP | 10.0 / ring mask `0xf` | confirmada na captura |
| COMPUTE IP | 10.0 / mask `0x7` | identidade/recursos observados; execução independente não demonstrada |
| Firmware SGPU | `2.23.0` | confirmada na captura |
| RTL | `0x0004ea15` | confirmada na captura |

## 4. Kernel e UAPI

Os caminhos fonte mais relevantes são:

```text
kernel/include/uapi/drm/sgpu_drm.h
kernel/drivers/gpu/drm/samsung/gpu/sgpu/
  amdgpu_drv.c
  amdgpu_cs.c
  amdgpu_vm.c
  amdgpu_gem.c
  amdgpu_object.c
  amdgpu_sched.c
  amdgpu_ring*.c
  amdgpu_ucode.c
  amdgpu_device.c
  exynos_gpu_interface.c
kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi
kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi
kernel/drivers/iommu/samsung/
kernel/drivers/dma-buf/heaps/samsung/
```

A nomenclatura AMDGPU-derived é relevante, mas não autoriza assumir compatibilidade binária ou semântica completa com upstream AMDGPU. A fonte contém caminhos de device match, BO, VM, CS, scheduler, firmware, GFX e MMHUB; cada um precisa ser confrontado com a imagem e os logs do aparelho.

## 5. Memória, IOMMU e VM

A coleta registra atividade real de VM e BO: `amdgpu_vm_pte_pde`, `amdgpu_vm_set_ptes`, `amdgpu_vm_update_ptes`, `amdgpu_vm_flush`, criação e movimentação de BO. Um probe anterior também concluiu criação de BO GTT de 64 KiB, mapeamento CPU, toque, VA map/unmap e close.

A fronteira ainda aberta é o acesso GPU controlado. A sequência observada em traces é:

```text
BO / DMA-BUF
  -> mapeamento SGPU/PIO
  -> amdgpu_vm_bo_cs
  -> amdgpu_vm_flush
  -> submissão
  -> execução no ring
  -> fence/retire/cleanup ainda a correlacionar completamente
```

## 6. Submissão GFX observada

Os traces P4.5, P4.6 e P4.12 registram `amdgpu_cs_ioctl`, `amdgpu_sched_run_job` e `amdgpu_ib_schedule` em `gfx_0.0.0`, com `num_ibs=3`. Isso confirma atividade de submissão GFX no caminho vendor observado.

O trace ainda não prova que o IB foi criado por um cliente novo, que o conteúdo foi decodificado de forma independente ou que houve readback controlado. Os eventos de fence clássica também não estavam completos em todas as coletas.

## 7. Android e loader

O `SurfaceFlinger` observado mapeia `/system/lib64/libvulkan.so`, `/vendor/lib64/hw/vulkan.samsung.so` e mantém FDs para `/dev/dri/renderD128`. O processo Termux está em outro mount/linker namespace e expôs somente `llvmpipe`.

```text
SurfaceFlinger
  -> libvulkan.so do sistema
  -> vulkan.samsung.so vendor
  -> libdrm_sgpu / renderD128
  -> SGPU kernel driver

Termux
  -> loader diferente / namespace diferente
  -> llvmpipe
  -> dlopen direto do ICD vendor bloqueado
```

Este é um dos principais bloqueios para injeção por loader: a presença do ICD vendor em `/vendor` não significa que um processo externo consiga carregá-lo.

## 8. OpenCL e compute

O inventário confirma `libOpenCL.so`, `libSGPUOpenCL.so` e exports como `clEnqueueNDRangeKernel`, `clEnqueueReadBuffer`, `clBuildProgram` e `clCreateProgramWithIL`. Isso é uma rota de investigação e referência de comportamento.

P5.04 não capturou CS, scheduler, IB compute, dispatch ou readback controlado. Portanto, o mapa classifica o compute como **superfície instalada, execução externa ainda não demonstrada**.

## 9. Hooks e pontos de integração classificados

| Ponto | Tipo | Estado | Uso possível |
| --- | --- | --- | --- |
| Device match `samsung-sgpu,samsung-sgpu` | kernel/platform | observado em fonte e runtime | selecionar o dispositivo correto |
| `sgpu_drm.h` | UAPI | fonte observada | definir cliente DRM e estruturas |
| `renderD128` | DRM render node | observado | ponto de abertura para cliente autorizado |
| BO/GEM/TTM/DMA-BUF | memória | atividade observada | alocação/import/export de recursos |
| VM/PTE/PDE/flush | memória virtual | atividade observada | mapear VA e sincronizar page tables |
| `amdgpu_cs_ioctl` | submit ioctl | observado em trace vendor | estudar contrato de command submission |
| `amdgpu_sched_run_job` | scheduler | observado em trace | identificar ciclo de execução |
| `amdgpu_ib_schedule` | IB | observado em trace | estudar formato e associação de IB |
| `gfx_0.0.0` | ring | observado | correlacionar fila/ring GFX |
| fence/syncobj | sincronização | parcial | concluir job e validar readback |
| `libdrm_sgpu.so` | user-space vendor | presença/export observado | referência e possível camada de compatibilidade, não ABI garantida |
| `vulkan.samsung.so` | ICD vendor | mapeado em SurfaceFlinger | referência de comportamento; não hook público |
| `libSGPUOpenCL.so` | compute vendor | presença/export observado | referência para primeiro compute; dispatch externo não provado |
| linker namespace Android | loader | bloqueio observado | precisa de processo suportado ou ponte controlada |
| `exynos_gpu_interface.c` | integração Samsung | símbolo/string observado | investigar callbacks e interface de plataforma |
| firmware load/reset | kernel/firmware | caminhos fonte observados | pré-requisito para qualquer cliente robusto |

## 10. Classificação dos pontos de falha

### Falhas de plataforma

Power-domain, clock, reset, IFPO, AFM, thermal, IRQ ou variante Device Tree incorreta podem impedir a GPU antes mesmo de um comando ser interpretado.

### Falhas de memória

BO criado mas não acessível pela GPU, IOMMU incorreto, VA com permissão errada, cache não sincronizado, buffer protegido ou page fault sem recuperação podem parecer falha de shader ou de packet.

### Falhas de submissão

Contexto, IB, ring, doorbell, chunk, prioridade, syncobj e fence ainda não estão todos descritos como contrato independente. Um símbolo `submit` não é prova de que qualquer pacote possa ser enviado com segurança.

### Falhas de Android

Namespace, SELinux, permissões de `/dev/dri`, manifest/HAL e dependências vendor bloqueiam a rota de carregar uma biblioteca em processo externo.

### Falhas de arquitetura

A Xclipse usa conceitos AMDGPU-derived, mas a integração Samsung é própria. Copiar implementações Vulkan de referência sem um backend de hardware compatível seria incorreto; o caminho provável exige reutilizar ou adaptar o kernel/DRM e implementar uma camada Xclipse específica.

## 11. Estado para uma futura porta de driver

```text
[Device Tree/plataforma]       CONFIRMADO EM GRANDE PARTE
            |
[DRM node + UAPI + memória]    PARCIALMENTE CONFIRMADO
            |
[submit GFX vendor]             CONFIRMADO NO CAMINHO EXISTENTE
            |
[cliente externo controlado]    NÃO DEMONSTRADO
            |
[compute/readback]              NÃO DEMONSTRADO
            |
[shader/ISA/compiler]           INDÍCIOS ESTÁTICOS
            |
[Vulkan/Mesa independente]      HIPÓTESE DE IMPLEMENTAÇÃO
```

## 12. Conclusão

O material já é suficiente para iniciar uma especificação técnica muito mais completa do kernel/DRM e para preparar um cliente de laboratório de baixo risco. Ainda não é suficiente para afirmar que um port direto de implementações Vulkan de referência funcionará.

A rota mais defensável é: fechar Device Tree e UAPI; implementar um cliente DRM/BO/VM read-only ou de mapeamento reversível; isolar um job GFX real; provar fence e readback; estudar compute; somente então decidir entre uma camada Vulkan própria, adaptação de backend Mesa ou outra arquitetura.

### Legenda

- **Confirmado:** observado diretamente ou sustentado por fonte e runtime.
- **Parcial:** parte do contrato observada, mas falta uma operação fim a fim.
- **Indício:** símbolo, string, export ou caminho sem execução controlada.
- **Hipótese:** rota de implementação ainda não validada.
- **Negativo:** uma tentativa específica não produziu a operação procurada; não prova que todas as alternativas falham.

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.

## Addendum de reprodução — identificação da camada DRM

O coleta de reprodução adiciona um probe direto com a tabela `card0/renderD128 → amdgpu` e `card1/renderD129 → exynos-drmdpu`. O mapa deve representar isso como identificação de nós e camada reportada. A cadeia física até `sgpu@22200000` ainda precisa de correlação por sysfs e platform device.

