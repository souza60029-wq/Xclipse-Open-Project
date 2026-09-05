# 5 dois meios — atualização dos primeiros cinco itens

**Fonte desta revisão:** `5doismeiosresultadosobtidos.zip`, SHA-256 `59ac6221373572024c0967d642f9ead1f970c6791ff2428cb5c49ae73a5fe7fc`.

Esta revisão separa cuidadosamente caminho identificado, símbolo existente, atividade correlacionada, submissão real, execução e readback. Um resultado só é promovido quando o pacote fornece contexto, saída bruta e uma interpretação que não dependa apenas do nome de um arquivo ou de uma função.

## Resumo de progresso

| Item | Estado após o novo pacote | O que foi realmente estabelecido |
| --- | --- | --- |
| 1. Loader Android e `VkPhysicalDevice` Samsung | **Parcialmente concluído** | O caminho de produção até o ICD Samsung foi observado no `SurfaceFlinger`: `vulkan.samsung.so` e `libvulkan.so` mapeadas, FDs em `/dev/dri/renderD128`; enumeração Vulkan detalhada no processo ainda não foi capturada. |
| 2. DRM, BO, VA, IOMMU e cache | **Fortalecido** | A identidade SGPU, `card0`, `renderD128`, `22200000.sgpu`, firmware e a biblioteca `libdrm_sgpu.so` foram correlacionados. O pacote também lista APIs de BO, VA, page fault e query; presença de símbolo não é execução. |
| 3. Contexto, ring, IB, fence e recuperação | **Mapeado, não executado** | O caminho de submission está mais bem delimitado por APIs `sgpu_cs_*`, `amdgpu_cs_*` e syncobjs. Ainda não há trace de um `submit` real feito pelo projeto nem readback controlado. |
| 4. Primeiro dispatch compute com readback | **Caminho real identificado, prova ainda aberta** | O serviço real `com.samsung.android.photoremasterservice:photoremasterservice` carrega Vulkan Samsung, `libdrm_sgpu.so`, `libOpenCL.so` e `libSGPUOpenCL.so`, usa `renderD128` e apresenta atividade SGPU correlacionada. Ainda não foi capturado um kernel próprio com BO antes/depois e valor validado. |
| 5. Correlação shader–binário–ISA | **Evidência estática forte, ISA ainda não provada** | O ICD stripped contém referências a SPIR-V, shader compiler, opcode translation, hardware opcode, `SCAsmEncoder`, `SCEmitter`, VOPC/VOP1/VOP2/VOP3/VOP3P e DPP8/DPP16. Algumas referências individuais são apenas logs; não há ainda shader real → binário → ISA nativa capturado em runtime. |

## 1. Loader Android e enumeração do `VkPhysicalDevice` Samsung

**Pergunta:** qual processo Android suportado carrega `/vendor/lib64/hw/vulkan.samsung.so`, em qual namespace, por qual manifest/HAL e com qual ABI?

**Nova evidência:** a coleta da Etapa 1 encontrou o `SurfaceFlinger` com PID 995 e, em uma captura posterior válida, mostrou `/vendor/lib64/hw/vulkan.samsung.so` e `/system/lib64/libvulkan.so` mapeadas no processo. O mesmo processo possuía FDs 11 e 12 apontando para `/dev/dri/renderD128`. O mount namespace do `SurfaceFlinger` era `mnt:[4026535441]`, enquanto o Termux observado estava em `mnt:[4026535972]`. Isso confirma uma fronteira real de processo/namespace e o caminho de produção até o SGPU.

A primeira parte da coleta falhou porque o PID foi consultado depois de morrer; por isso `/proc/995/status`, `/proc/995/maps` e namespaces apareceram como inexistentes naquela captura inicial. A captura adicional, feita enquanto o processo era válido, é a evidência que deve ser usada.

**Classificação:** **caminho de produção confirmado; enumeração Vulkan detalhada ainda pendente**. A presença do ICD mapeado no `SurfaceFlinger` não prova, isoladamente, que o projeto capturou `vkEnumeratePhysicalDevices` ou um `VkPhysicalDevice` Samsung naquele processo.

**Próximo teste:** instrumentar ou observar o processo suportado sem substituir bibliotecas, capturando chamadas de criação/enumeração, vendor/device ID, extensões e o vínculo com o serviço gráfico.

## 2. Contrato DRM, BO, VA, IOMMU e cache

**Nova evidência:** a Etapa 2 fechou o mapeamento de `card0 → sgpu`, `card1 → exynos-drm`, `/dev/dri/renderD128` e `/dev/dri/renderD129`. O nó SGPU está em `/sys/devices/platform/22200000.sgpu`, com árvore DRM em `/sys/devices/platform/22200000.sgpu/drm/renderD128`, compatível com `samsung-sgpu,samsung-sgpu` em `/sgpu@22200000`.

A Etapa 3 confirmou `sgpu_governor`, frequências disponíveis de 252000 a 1095000, SGPU firmware `2.23.0`, RTL `0x0004ea15`, ME `0x5`, MEC `0x4`, PFP `0x7` e RLC `0x1`. O `v4` também identifica `libdrm_sgpu.so` e símbolos para `sgpu_bo_alloc`, `sgpu_bo_export`, `sgpu_bo_import`, `sgpu_bo_list_create`, `sgpu_bo_va_op`, `sgpu_create_bo_from_user_mem`, `sgpu_query_gpu_page_faults` e `sgpu_va_range_alloc`.

**Classificação:** **contrato estrutural e bibliotecas correlacionados; execução de cada API ainda não demonstrada**. O probe anterior continua sendo a prova direta de BO GTT, mapeamento de CPU e VA. Strings e exports ampliam o mapa, mas não devem ser descritos como chamadas realizadas.

**Próximo teste:** correlacionar uma chamada real do serviço Samsung com o FD SGPU, o BO/VA correspondente, flags de memória e eventual page fault, preservando a recuperação.

## 3. Contexto, ring, IB, fence e recuperação

**Nova evidência:** a preparação da Etapa 4 lista `amdgpu_cs_ctx_create`, `amdgpu_cs_submit`, `amdgpu_cs_submit_raw`, `amdgpu_cs_wait_fences`, criação/import/export de syncobj e equivalentes `sgpu_cs_ctx_create`, `sgpu_cs_submit`, `sgpu_cs_submit_raw`, `sgpu_cs_wait_fences`, `sgpu_cs_syncobj_*`.

Isto é um avanço importante no mapa do caminho de submission. Ainda assim, o próprio relatório da Etapa 4 registra que o requisito forte não foi demonstrado:

> `BO antes → submit real → execução GPU → sincronização/fence → BO depois/readback → recuperação`

**Classificação:** **interfaces de contexto/submission/sincronização localizadas; execução de submission do projeto não confirmada**. O nome `sgpu_cs_submit` em uma biblioteca ou relatório é capability disponível, não prova de uma chamada bem-sucedida.

**Próximo teste:** observar primeiro uma operação real do Photo Remaster com instrumentação não invasiva; só depois desenhar um submit mínimo próprio com timeout, fence, consulta de reset e rollback.

## 4. Primeiro dispatch compute com readback

**Nova evidência:** o processo correto não era `com.samsung.android.app.remaster`. A investigação encontrou:

- `com.sec.android.mimage.photoretouching`, com `/dev/dri/renderD128` e `vulkan.samsung.so`/`libdrm_sgpu.so`;
- `com.samsung.android.photoremasterservice:photoremasterservice`, com `/dev/dri/renderD128`, `/dev/dri/card0`, `vulkan.samsung.so`, `libdrm_sgpu.so`, `libOpenCL.so` e `libSGPUOpenCL.so`.

A biblioteca OpenCL contém APIs para contexto, filas, buffers, imagens, programas, kernels, `clEnqueueNDRangeKernel`, `clEnqueueTask`, `clFlush`, `clFinish`, eventos, barreiras e `clEnqueueReadBuffer`. Durante atividade do Photo Remaster, `runtime_status` apareceu como `active`, `cur_freq` variou de 252000 para 500000 e os FDs continuaram apontando para `renderD128`.

**Classificação:** **o caminho real Samsung de compute está identificado e há correlação operacional com o SGPU; o primeiro dispatch verificável continua aberto**. Símbolos OpenCL, frequência variável e atividade do processo não provam que um kernel específico foi submetido, concluído e validado.

**Próximo teste:** capturar uma operação controlada do Photo Remaster com entrada conhecida, saída conhecida e observação de evento/fence. Em paralelo, determinar se é possível executar um kernel mínimo por uma interface suportada, sem submit cego e sem reutilizar command buffers opacos.

## 5. Correlação shader–binário–ISA

**Nova evidência:** a análise estática do `vulkan.samsung.so` encontrou strings e referências relacionadas a:

- `ShaderCompile`, SPIR-V, pipeline e `COMPUTE_SHADER_CHKSUM`;
- `MGFX1_GEN`, `MGFX2_GEN`, `MGFX3_GEN`, `MGFX4_GEN` e `gfx10_4_GEN`;
- `SCEmitterGFX103.cpp`, `SCAsmEncoder.cpp`, `SCAsmEncoder.hpp`;
- `GetOpcode`, `gen_opcode`, `XlateOpcode`, `GetHwOpcode`;
- `EncodeDPP`, `EncodeSDWA`, `EncodeWaitDepctr`, `EncodeImmediateBuffer`, `EncodeMaccDelay`;
- `SCEmitVOp1`, `SCEmitScratch`, `SCEmitFlat`, `SCEmitVOp3`;
- famílias VOPC, VOP1, VOP2, VOP3, VOP3P, DPP8 e DPP16;
- mensagens `No encoding found for instruction pattern`, `Invalid encoding`, `Unexpected operand for this encoding` e `SPIR-V OpCode unsupported`.

Também houve uma primeira disassembly AArch64 real da seção `.text`, localizada corretamente no endereço virtual `0x143fb30`, e o ELF foi identificado como AArch64 stripped com BuildID `7b6134ba45f561f28b006e07ef075b4d4c429bcd`.

A análise corrigiu três riscos de interpretação. Primeiro, `gfx10_4_GEN` não prova que XO940 seja GFX10.4. Segundo, referências a `AMD Shader Compiler`, `SCAsmEncoder.cpp` e `SCEmitVOp3` podem ser apenas logging quando a referência termina em `__android_log_print`. Terceiro, nomes de strings não são símbolos de função em um ELF stripped.

**Classificação:** **infraestrutura interna de compilação/encoding é uma hipótese fortemente sustentada por análise estática; ISA efetivamente utilizada pelo XO940 ainda não foi capturada**. A cadeia `SPIR-V → IR/tradução → opcode → hardware opcode → emitter → encoder → binary` é uma inferência conjunta, não uma prova de cada chamada.

**Próximo teste:** obter shader controlado pelo caminho Samsung real, capturar binário ou metadados produzidos/consumidos, correlacionar uma instrução com execução e comparar revisões. Não publicar o `vulkan.samsung.so` proprietário no Git.

## Estado dos cinco itens

A ordem de trabalho permanece **1 → 2 → 3 → 4 → 5**, mas o Item 1 agora tem caminho de produção identificado, o Item 4 tem o caminho OpenCL/Photo Remaster identificado e o Item 5 possui evidência estática substancial do backend interno. Nenhum desses avanços deve ser convertido em “driver aberto funcionando” ou “ISA decodificada” antes da captura de execução correspondente.

## Referências locais

- `STATUS.md` — ledger consolidado do projeto.
- `reports/new-results-analysis.md` — análise do ZIP recebido nesta revisão.
- `docs/21-android-loader.md` — loader e namespaces.
- `docs/19-compute-pipeline.md` — dispatch e readback.
- `docs/14-shader-isa.md` — evidência estática do compiler/encoding.
- `docs/25-kernel-drm-uapi.md` — DRM/UAPI e SGPU.
- `docs/23-icd-and-manifests.md` — ICD e caminho Android.
