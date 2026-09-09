# Documentation Index

The documentation is layered from platform and kernel foundations to execution, ISA, compiler, Vulkan, Android integration, security, and validation. The numbered chapters follow the investigation order in `ROADMAP.md`; they are not claims that every layer is already understood.

| Layer | Chapters |
| --- | --- |
| Scope and evidence | `00-project-scope.md`, `validation-and-evidence.md` |
| Platform and kernel | `01`–`13`, `25`, `28` |
| Execution and ISA | `14`–`19`, `26` |
| Vulkan and Android | `20`–`24` |
| Uncertainty and results | `27`, `29` |

Every chapter should link to raw artifacts and experiment reports when a statement moves beyond the initial plan.

The repository also publishes the **5 dois meios** plan in `artifacts/PLANO_Xclipse_Open_940.pdf` and the separate technical `artifacts/RAMIFICACAO_Xclipse_Open_940.pdf`. The second PDF contains only the mapped Xclipse/kernel/GPU/firmware/DRM/Vulkan/OpenCL structure; it is not the repository directory tree.

The 2026-09-06 public study is [`reports/estudo-detalhado-xclipse-940-2026-09-06.md`](../reports/estudo-detalhado-xclipse-940-2026-09-06.md). The hardware-only Device Tree map is [`source-analysis/device-tree-technical-map.md`](../source-analysis/device-tree-technical-map.md), and the evidence classification is [`source-analysis/xclipse-2026-09-06-evidence-map.md`](../source-analysis/xclipse-2026-09-06-evidence-map.md).

## Quasar reproducibility branch

The separate [`quasar` branch](https://github.com/souza60029-wq/Xclipse-Open-Project/tree/quasar) contains the reproducibility workspace, raw experiments, source files, logs, hashes and implementation roadmap for the Quasar/Xclipse path. Its dedicated documents are [`quasar/DOCUMENTACAO_QUASAR_Xclipse.md`](../quasar/DOCUMENTACAO_QUASAR_Xclipse.md), [`quasar/MAPA_QUASAR_Xclipse.md`](../quasar/MAPA_QUASAR_Xclipse.md) and [`quasar/REPRODUCING.md`](../quasar/REPRODUCING.md). These are branch documentation, not additional release downloads.
