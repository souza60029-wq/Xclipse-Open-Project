# Pontos de entrada para um driver independente da Xclipse 940

**Este arquivo é privado por enquanto. Não foi publicado no GitHub.**

## Objetivo

Avaliar onde um driver de espaço de usuário inspirado em Mesa/RADV poderia se conectar ao sistema existente, sem assumir compatibilidade direta com RADV, Turnip ou AMDGPU upstream.

## Hierarquia de rotas

### Rota A — cliente DRM/SGPU sobre o kernel existente

Esta é a rota mais realista para o primeiro protótipo. O kernel já expõe um render node SGPU e possui uma UAPI em `kernel/include/uapi/drm/sgpu_drm.h`. A implementação deveria começar como uma biblioteca de laboratório, sem Vulkan, cobrindo descoberta, abertura de `renderD128`, query, BO, VA, sincronização e cleanup.

**Pontos de entrada candidatos:**

- `renderD128`;
- ioctls e estruturas de `sgpu_drm.h`;
- caminhos BO/GEM/TTM/DMA-BUF;
- VM/PTE/PDE e flush;
- context/CS/IB/ring;
- fences/syncobjs;
- firmware/reset somente por interfaces já suportadas pelo kernel.

**Por que é a melhor primeira rota:** evita substituir o driver kernel e permite testar o contrato que já sustenta os jobs vendor. O bloqueio atual é que submit, fence e readback próprios ainda não foram demonstrados.

### Rota B — biblioteca compatível com libdrm_sgpu

A `libdrm_sgpu.so` possui presença e símbolos relacionados a BO, VA, context, submission, reset, faults, fences e sync. Ela pode ser usada como referência de nomes, parâmetros e sequência de chamadas.

**Não tratar como ABI garantida:** exports e strings não provam que a biblioteca possa ser redistribuída, carregada fora do namespace vendor ou usada sem inicialização privada. O port deve criar uma camada própria somente após revisar licença e observar chamadas em um processo suportado.

### Rota C — backend compute experimental

A presença de `libOpenCL.so` e `libSGPUOpenCL.so` fornece uma rota para investigar um primeiro workload compute. O programa de laboratório deve separar identificação, contexto, fila, compilação, dispatch e readback.

**Gate:** P5.04 não demonstrou compute. Não implementar um backend Vulkan sobre a suposição de que `clEnqueueNDRangeKernel` já está disponível para um cliente externo.

### Rota D — camada Vulkan/Mesa própria

Somente depois de fechar memória e submit. A camada poderia implementar inicialmente um subset pequeno, como device discovery, buffer, compute e sincronização. O backend deve traduzir para o contrato SGPU observado, não para uma suposta ABI AMDGPU universal.

**RADV:** pode servir como referência de organização de driver Vulkan, gerenciamento de recursos, NIR/LLVM e sincronização, mas não é um plug-in automático para Xclipse.

**Turnip:** pode servir como referência de estratégia para uma GPU móvel com kernel/firmware vendor, mas a comparação arquitetural não substitui conhecer os packets, ISA e ABI Xclipse.

## Hooks reais versus falsos hooks

| Ponto | Classificação | Decisão |
| --- | --- | --- |
| `sgpu_drm.h` | hook de contrato observado | estudar primeiro |
| `renderD128` | endpoint DRM observado | cliente de laboratório |
| `amdgpu_cs_ioctl` | chamada observada em processo vendor | correlacionar, não copiar cegamente |
| `amdgpu_sched_run_job` | hook interno/tracing observado | usar para análise |
| `amdgpu_ib_schedule` | hook interno/tracing observado | estudar associação de IB |
| `gfx_0.0.0` | ring observado | validar context/seqno |
| `sgpu_cs_submit` em símbolo/export | indício de interface | não considerar submit funcional sem execução própria |
| exports OpenCL | superfície instalada | não considerar ABI aberta |
| `vulkan.samsung.so` | ICD vendor mapeado em SurfaceFlinger | referência observacional |
| `exynos_gpu_interface.c` | integração fonte Samsung | investigar callbacks e limites |
| `sgpu@22200000` | recurso Device Tree | não é hook de user-space |
| strings de ISA/compiler | evidência estática | não é backend implementado |

## Esqueleto recomendado do protótipo

```text
xclipse-lab/
├── include/
│   └── xclipse_drm_compat.h
├── src/
│   ├── device.c       # renderD128, DRM version, identity
│   ├── bo.c           # BO/GEM/DMA-BUF, sem submit inicial
│   ├── vm.c           # VA map/unmap e flush documentado
│   ├── sync.c         # syncobj/fence quando suportado
│   ├── submit.c       # bloqueado até contrato IB ser comprovado
│   └── recovery.c     # timeout/abort/cleanup
├── tests/
│   ├── query-only/
│   ├── bo-map-cleanup/
│   └── controlled-submit/  # privado, não publicar ainda
└── docs/
    ├── uapi-matrix.md
    ├── memory-lifecycle.md
    └── submission-evidence.md
```

## Gates antes de usar RADV/Mesa

1. Abrir `renderD128` e identificar o dispositivo sem depender do ICD vendor.
2. Criar e destruir BO de modo repetível.
3. Mapear VA, fazer flush e limpar sem vazamento.
4. Correlacionar context, seqno, ring e fence em um processo próprio.
5. Executar um IB controlado e validar um buffer de saída.
6. Demonstrar um compute mínimo ou localizar o bloqueio.
7. Só então avaliar a tradução para uma API Vulkan/Mesa.

## Riscos principais

O maior risco é confundir a semelhança de nomenclatura AMDGPU com compatibilidade de packets. O segundo é depender de bibliotecas vendor sem controlar namespace e permissões. O terceiro é enviar comandos opacos antes de haver timeout e recuperação documentados. O quarto é interpretar um job de SurfaceFlinger/Photo Remaster como se fosse um job do nosso cliente.

## Decisão provisória

A recomendação é **não começar por um fork direto de RADV ou Turnip**. Começar por um cliente DRM/SGPU mínimo e instrumentado. Se o cliente conseguir BO → VM → submit → fence → readback, então o material coletado permitirá decidir qual parte de Mesa é reutilizável e qual backend Xclipse precisa ser escrito.

## Evidência que falta para mudar a decisão

- primeira submissão GFX controlada por processo próprio;
- fence/retire correlacionado;
- readback com assinatura conhecida;
- compute mínimo;
- identificação do formato de IB/packet;
- pelo menos uma relação confiável entre shader de entrada, binário e execução.

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
