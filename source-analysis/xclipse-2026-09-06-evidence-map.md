# Mapa público de evidências — coleta de 2026-09-06

Este mapa conecta famílias de artefatos a conclusões permitidas. Ele não redistribui os arquivos crus.

| Família | Observação | Conclusão permitida | Não concluir |
| --- | --- | --- | --- |
| R1 | `sgpu@22200000`, filhos SGPU, IRQs, phandle G3DCORE, memória e IOMMU | a plataforma e seus vínculos foram observados | que todos os registradores já estejam decodificados |
| R2 | nós `/dev/dri/card*` e `renderD*` e permissões | existem nós DRM acessíveis no snapshot | qual nó é o alvo sem identificação adicional |
| R3/R5 | `vulkan.samsung.so`, `libdrm_sgpu.so`, OpenCL e SONAME/NEEDED | a pilha vendor está instalada | que bibliotecas vendor sejam uma ABI pública reutilizável |
| P2 | PTE, VM update, VM flush, BO create/move e correlação por PID | há atividade real de VM/BO | formato completo de page table ou recuperação de fault |
| P4.5/P4.6/P4.12 | CS ioctl, scheduler run job, IB schedule e `gfx_0.0.0` | há submissão GFX observada | que o workload controlado foi executado e produziu readback |
| P5.01 | exports OpenCL e bibliotecas | existe uma superfície OpenCL vendor | que um dispatch externo foi concluído |
| P5.04 | trace sem eventos de CS/scheduler/IB compute | não houve captura compute demonstrativa | sucesso de compute |
| P5.05 | tracepoints SGPU e AMDGPU enumeráveis | há pontos de observação úteis | que a enumeração seja um teste de hardware |

| xclipselogs A1.5/STRIKE | cliente próprio: render node, GEM, VA, BO_LIST, contexto e uma aceitação de CS | KMD aceitou uma entrada CS específica; não concluir execução/fence/readback |

## Cadeias comprovadas

```text
Device Tree sgpu@22200000
  -> G3DCORE power-domain / IRQ / regiões MMIO
  -> VM/BO/PTE e flush
  -> amdgpu_cs_ioctl
  -> amdgpu_sched_run_job
  -> amdgpu_ib_schedule em gfx_0.0.0
```

A cadeia acima é observada no caminho vendor/SGPU fornecido. Ela não representa ainda um driver novo.

## Cadeias ainda abertas

```text
cliente externo -> UAPI mínima -> BO/VM -> IB controlado
  -> fence de conclusão -> buffer de saída
```

```text
OpenCL externo -> contexto -> fila -> NDRange -> readback
```

Essas duas cadeias são objetivos de estudo, não resultados publicados.

## Política de publicação

Os P e scripts são material de laboratório. O GitHub recebe a classificação e a síntese, mas não os testes baixáveis.

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.

## Quasar addendum — probe DRM direto

O pacote Quasar acrescenta o experimento `X940-001c`, um probe C que chama `DRM_IOCTL_VERSION` diretamente e preserva fonte, binário, ambiente, stdout, stderr e hashes. No SM-S721B analisado, os resultados foram:

| Nó | Resultado `DRM_IOCTL_VERSION` | Interpretação permitida |
| --- | --- | --- |
| `/dev/dri/card0` | `name=[amdgpu]`, `226:0` | camada DRM reportada; não provar GPU AMD física |
| `/dev/dri/renderD128` | `name=[amdgpu]`, `226:128` | render node da camada reportada; não provar RADV compatível |
| `/dev/dri/card1` | `name=[exynos-drmdpu]`, `226:1` | DRM de display Samsung |
| `/dev/dri/renderD129` | `name=[exynos-drmdpu]`, `226:129` | nó associado à camada de display reportada |

O vínculo físico recomendado continua sendo `node → major/minor → sysfs → driver → platform device → sgpu@22200000`. O experimento `X940-001b` também documenta que `Download/Quasar` pode ser `noexec`; esse resultado é uma restrição operacional de reprodução, não uma falha de GPU.
