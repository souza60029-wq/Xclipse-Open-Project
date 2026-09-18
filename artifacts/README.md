# Artefatos públicos do XO940

A distribuição pública contém documentação técnica consolidada e mapas de arquitetura da Samsung Xclipse 940, incluindo a separação entre GPU Xclipse e NPU Exynos/NNAPI. O conteúdo foi derivado de coletas reais e revisado para remover dados pessoais, logs brutos, bibliotecas vendor, firmware e arquivos de ambiente.

| Entregável | Conteúdo |
| --- | --- |
| `DOCUMENTACAO_Xclipse_940_COMPLETA.pdf` | Plataforma, Device Tree, SGPU/DRM, GEM/BO/VA/IOMMU, CS/IB, scheduler, fences, Android, bibliotecas, variantes, NPU/NNAPI e política de reprodução. |
| `MAPA_E_RAMIFICACAO_Xclipse_940.pdf` | Mapa de arquitetura, ramificação técnica, ramo NPU/NNAPI, caminhos operacionais, legenda cromática e investigação de falhas. |
| `Xclipse-Open-Project-public-final.zip` | Os dois PDFs e os mapas PNG visuais, incluindo o mapa principal e o mapa de falhas. |
| `NPU-ENN-rootless-2026-09-18.zip` | Pacote sanitizado com o relatório público da execução NNAPI/ENN sem root, o documento técnico e os hashes de proveniência. |
| `NPU-EXYNOS-2400-EVIDENCIAS-COMPLETO.pdf` | Documento detalhado com o inventário dos itens 01–18, arquitetura, serviços, VINTF, SELinux, endpoints, Device Tree, operações, benchmarks, bloqueios e plano de validação. |
| `MAPA_NPU_EXYNOS_2400_DETALHADO.pdf` | Mapa vetorial ampliável com a estrada NNAPI confirmada, HAL, kernel, segurança, endpoint direto, operações e investigação aberta. |
| `ARQUITETURA_XO940_COMPLETA.pdf` | Documento-mestre da arquitetura completa do XO940: plataforma, GPU, DRM, memória, VM, submissão, firmware, sincronização, Android, Vulkan, OpenCL, segurança, variantes e ramo NPU separado. |
| `MAPA_XO940_ARQUITETURA_COMPLETA.pdf` | Mapa vetorial ampliável com as conexões entre Device Tree, kernel/DRM, memória, execução, Android e o ramo separado NPU/NNAPI/ENN. |
| `XO940-ARQUITETURA-COMPLETA-2026-09-18.zip` | Pacote com a documentação-mestre, mapa vetorial, pré-visualização e referências arquiteturais relacionadas. |

## Classificação

Os documentos diferenciam observação confirmada, confirmação por fonte, resultado parcial, indício, hipótese e resultado negativo específico. A nova evidência rootless inclui compilação, execução e readback numérico correto no `IDevice/enn`; um inicializador, uma enumeração ou um ioctl aceito continua não sendo apresentado como execução GPU ou NPU sem validação.

## Limites

Fontes Samsung são tratadas como referência de proveniência. O repositório não redistribui automaticamente código-fonte, firmware, bibliotecas vendor, executáveis, dumps, credenciais, identificadores ou logs brutos. Experimentos de aplicativos e subsistemas Exynos permanecem separados da documentação operacional da GPU Xclipse.
