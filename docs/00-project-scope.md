# Escopo e terminologia

## Finalidade

O Xclipse Open Project documenta o contrato hardware/software das GPUs Samsung Xclipse para que desenvolvedores possam estudar a plataforma com evidência reproduzível. O escopo começa por observação, inventário e mapas técnicos. Implementações só são consideradas quando o contrato, a licença e a recuperação estiverem suficientemente documentados.

O alvo de referência é a Xclipse 940 associada ao Samsung SM-S721B. XO940 é o nome interno do projeto de documentação. Revisões e gerações futuras devem permanecer separadas até que a compatibilidade seja demonstrada.

## Vocabulário técnico

A **Device Tree** é a descrição do kernel para regiões de registradores, interrupções, domínios de energia, clocks, DMA, coerência, relações de IOMMU, dependências de reset, strings `compatible` e propriedades de revisão. Ela não é a árvore de diretórios do repositório e não é o driver completo.

O trabalho combina documentação de GPU, bring-up de DRM, análise de memória, observação de filas, investigação de ISA, compiler, integração Vulkan e validação entre revisões. Um componente chamado loader, ICD, layer ou backend deve ser tratado como uma camada distinta até que sua função seja observada diretamente.

## Incluído

O projeto cobre identificação de hardware, revisões de chip, Device Tree, integração de plataforma, DRM, UAPI, GEM, BO, DMA-BUF, IOMMU, memória virtual, faults, scheduler, fences, sync objects, reset, filas GFX/compute/DMA, referências de firmware, clocks, energia, OPP, comportamento térmico, namespaces Android, manifests ICD, SELinux, HWC, packets, registradores, shaders, ISA preliminar, compiler, mapeamento Vulkan, layers de diagnóstico e validação entre revisões.

## Não concluído

Enumeração de API, criação de objetos, retorno de `VK_SUCCESS`, existência de biblioteca, compilação de teste ou presença de uma extensão não constituem execução GPU. Compatibilidade com jogos, WSI, apresentação, todos os formatos, todas as filas e todas as revisões permanece fora de qualquer afirmação automática.

## Regra de evidência

| Artefato ou ação | Pode estabelecer | Não estabelece sozinho |
| --- | --- | --- |
| Inicialização de teste | Ambiente preparado. | Execução de GPU ou resultado correto. |
| Enumeração Vulkan | Um caminho de loader expôs objetos. | Submit, shader, rendering ou apresentação. |
| Consulta de capacidade | A implementação reportou propriedade ou extensão. | Execução correta em todos os estados. |
| Alocação e mapeamento | Um caminho de alocação e visibilidade de CPU funcionou. | A GPU acessou o recurso. |
| Gravação de comandos | O caminho aceitou a construção. | Submit, execução ou conclusão. |
| Submit | A API aceitou uma requisição. | O firmware executou o trabalho. |
| Fence | O objeto de sincronização alcançou um estado. | Que aquele trabalho específico causou o estado. |
| Readback esperado | Uma operação fim a fim específica passou. | Suporte geral a formatos, filas ou workloads. |

## Referências

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"

[1] [2]
