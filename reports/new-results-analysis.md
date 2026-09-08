# Análise do pacote `5doismeiosresultadosobtidos.zip`

**Hash SHA-256:** `59ac6221373572024c0967d642f9ead1f970c6791ff2428cb5c49ae73a5fe7fc`
**Tamanho comprimido:** aproximadamente 14 MiB
**Data de análise:** 05 de setembro de 2026

O pacote foi lido e extraído fora do Git. O binário proprietário `vulkan.samsung.so` não foi copiado para o repositório. O relatório abaixo registra somente metadados, caminhos e conclusões derivadas.

## Conteúdo recebido

| Arquivo | Tratamento | Valor da evidência |
| --- | --- | --- |
| `XO940_ETAPA1_DEFINITIVO.txt` | Lido integralmente por seções | Identificação inicial do ICD, SurfaceFlinger, namespaces e nós DRM; a primeira consulta de PID sofreu race condition, mas a captura adicional trouxe mapas válidos. |
| `XO940_CHECKPOINT_ETAPA_2_DEFINITIVO.txt` | Lido integralmente | Fecha o mapeamento `card0 → sgpu`, `card1 → exynos-drm`, `renderD128` e `renderD129`. |
| `ETAPA_X_IDENTIFICACAO_SGPU.txt` | Lido integralmente | Confirma SGPU, `22200000.sgpu`, firmware 2.23.0, RTL e frequências/devfreq. |
| `ETAPA_3_COMPLETA.txt` | Normalizado e analisado por seções e linhas de evidência | Coleta ampla de SurfaceFlinger, DRM, processos, bibliotecas e atividade gráfica; contém muito log incidental e não deve ser tratado como um único teste. |
| `Etapa_4_SGPU_Xclipse940_LOGS.zip` | Extraído e lido | Identifica os processos reais do Photo Remaster, bibliotecas Vulkan/OpenCL, FDs SGPU e limites da prova de dispatch. |
| `v4.txt` | Lido integralmente | Lista APIs e símbolos de libdrm/SGPU, OpenCL e Vulkan; é uma etapa de preparação e inventário, não um trace de submissão. |
| `Etapa 5.txt` | Lido integralmente | Registra strings, offsets, disassembly AArch64, referências de opcode/encoding e as correções contra interpretações excessivas. |
| `Etapa 5 documentada.pdf` | Convertido para texto e confrontado com `Etapa 5.txt` | Confirma a conclusão graduada: infraestrutura estática forte, sem prova de ISA nativa em runtime. |
| `vulkan.samsung.so` | Mantido fora do Git; apenas metadados locais | ELF AArch64 stripped, 44.423.944 bytes, BuildID `7b6134ba45f561f28b006e07ef075b4d4c429bcd`. |

## Conclusões promovidas

### Loader e caminho de produção

A coleta válida do `SurfaceFlinger` mostrou `vulkan.samsung.so` e `/system/lib64/libvulkan.so` mapeadas no processo, além de FDs para `/dev/dri/renderD128`. A diferença entre o mount namespace do `SurfaceFlinger` e o do Termux explica por que o resultado de `llvmpipe` no Termux não invalida a presença do ICD Samsung no sistema. O Item 1 foi promovido para **caminho de produção confirmado**, mas a enumeração Vulkan detalhada ainda não foi capturada.

### SGPU e DRM

O mapeamento `card0 → sgpu` e `renderD128` foi confirmado com sysfs, Device Tree e driver. `card1 → exynos-drm` e `renderD129` permanecem separados. A identidade de plataforma `/sgpu@22200000`, o driver `/sys/bus/platform/drivers/sgpu`, firmware SGPU `2.23.0`, RTL `0x0004ea15` e frequências devfreq foram registrados.

### OpenCL e Photo Remaster

O processo correto de serviço foi corrigido para `com.samsung.android.photoremasterservice:photoremasterservice`. Esse processo carregou simultaneamente `vulkan.samsung.so`, `libdrm_sgpu.so`, `libOpenCL.so` e `libSGPUOpenCL.so`, mantendo FDs em `renderD128`. A API OpenCL expõe contexto, fila, buffers, kernels, `clEnqueueNDRangeKernel`, sincronização e readback. Isso confirma um caminho real de compute do vendor, mas não é prova de um kernel próprio concluído nem de readback controlado pelo projeto.

### Compiler e encoding

A análise do ICD encontrou strings e contexto relacionados a SPIR-V, shader compiler, opcode translation, hardware opcode, emitter, encoder, VOPC/VOP1/VOP2/VOP3/VOP3P e DPP8/DPP16. A análise também mostrou que referências individuais como `AMD Shader Compiler`, `gfx10_4_GEN`, `SCAsmEncoder.cpp` e `SCEmitVOp3` podem aparecer em mensagens de log. Portanto a classificação correta é **infraestrutura estática fortemente sustentada**, não “ISA Xclipse decodificada” e não “backend runtime comprovado”.

## Negativas importantes

O pacote não fornece um trace comprovando a cadeia completa `BO antes → submit real → execução GPU → fence → BO depois/readback → recuperação`. A presença de `sgpu_cs_submit`, `amdgpu_cs_submit`, `clEnqueueNDRangeKernel` ou `clEnqueueReadBuffer` como símbolos não altera essa negativa. Também não fornece uma relação runtime entre shader SPIR-V controlado, binary gerado e instrução ISA nativa da Xclipse 940.

## Política de publicação

O binário `vulkan.samsung.so`, bibliotecas vendor e logs crus permanecem fora do Git. O repositório publica apenas conclusões, metadados, caminhos, hashes e documentação de proveniência. Qualquer código Samsung continua sujeito a revisão de licença e não deve ser redistribuído por estar presente no pacote de trabalho.

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. O relatório sanitizado está em [`reports/xclipselogs-2026-09-07-analysis.md`](reports/xclipselogs-2026-09-07-analysis.md). Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
