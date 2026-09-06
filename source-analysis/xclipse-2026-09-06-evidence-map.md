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
