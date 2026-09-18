# Artefatos públicos do XO940

A distribuição pública contém documentação técnica consolidada e mapas de arquitetura da Samsung Xclipse 940, incluindo a separação entre GPU Xclipse e NPU Exynos/NNAPI. O conteúdo foi derivado de coletas reais e revisado para remover dados pessoais, logs brutos, bibliotecas vendor, firmware e arquivos de ambiente.

| Entregável | Conteúdo |
| --- | --- |
| `DOCUMENTACAO_Xclipse_940_COMPLETA.pdf` | Plataforma, Device Tree, SGPU/DRM, GEM/BO/VA/IOMMU, CS/IB, scheduler, fences, Android, bibliotecas, variantes, NPU/NNAPI e política de reprodução. |
| `MAPA_E_RAMIFICACAO_Xclipse_940.pdf` | Mapa de arquitetura, ramificação técnica, ramo NPU/NNAPI, caminhos operacionais, legenda cromática e investigação de falhas. |
| `Xclipse-Open-Project-public-final.zip` | Os dois PDFs e os mapas PNG visuais, incluindo o mapa principal e o mapa de falhas. |
| `NPU-ENN-rootless-2026-09-18.zip` | Pacote sanitizado com o relatório público da execução NNAPI/ENN sem root, o documento técnico e os hashes de proveniência. |

## Classificação

Os documentos diferenciam observação confirmada, confirmação por fonte, resultado parcial, indício, hipótese e resultado negativo específico. A nova evidência rootless inclui compilação, execução e readback numérico correto no `IDevice/enn`; um inicializador, uma enumeração ou um ioctl aceito continua não sendo apresentado como execução GPU ou NPU sem validação.

## Limites

Fontes Samsung são tratadas como referência de proveniência. O repositório não redistribui automaticamente código-fonte, firmware, bibliotecas vendor, executáveis, dumps, credenciais, identificadores ou logs brutos. XLIA e experimentos de subsistemas Exynos permanecem separados da documentação operacional da GPU Xclipse.
