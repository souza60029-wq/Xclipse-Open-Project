# Índice de documentação

A documentação é organizada da plataforma e do kernel até execução, ISA, compiler, Vulkan, Android, segurança e validação. A numeração indica a ordem de investigação; não representa compreensão completa de nenhuma camada.

| Camada | Capítulos |
| --- | --- |
| Escopo e evidências | `00-project-scope.md`, `validation-and-evidence.md` |
| Plataforma e kernel | `01`–`13`, `25`, `28` |
| Execução e ISA | `14`–`19`, `26` |
| Vulkan e Android | `20`–`24` |
| Incerteza e resultados | `27`, `29` |

A documentação pública usa a política de evidências para distinguir inicialização, probe, smoke test, execução, regressão e teste negativo. Logs crus e scripts de captura permanecem fora da release.

## Documentos centrais

- [`README.md`](../README.md): escopo, coleta e estado resumido.
- [`STATUS.md`](../STATUS.md): ledger técnico atualizado.
- [`source-analysis/device-tree-technical-map.md`](../source-analysis/device-tree-technical-map.md): mapa da Device Tree e da plataforma.
- [`source-analysis/xclipse-2026-09-06-evidence-map.md`](../source-analysis/xclipse-2026-09-06-evidence-map.md): mapa público de evidências.
- [`artifacts/MAPA_Xclipse_940_COMPLETO.md`](../artifacts/MAPA_Xclipse_940_COMPLETO.md): mapa textual consolidado.
- [`public/MAPA_E_RAMIFICACAO_Xclipse_940.md`](../public/MAPA_E_RAMIFICACAO_Xclipse_940.md): ramificação técnica e legenda visual.

## Proveniência

Cada derivado deve manter o identificador do experimento, aparelho, data, ambiente, classificação, hash e limite da interpretação. Os projetos XLIA e Exynos NPU permanecem separados porque possuem objetivos e superfícies de coleta diferentes.
