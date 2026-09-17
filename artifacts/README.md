# Artefatos públicos do XO940

A distribuição pública contém documentação técnica consolidada e mapas de arquitetura da Samsung Xclipse 940. O conteúdo foi derivado de coletas reais e revisado para remover dados pessoais, logs brutos, bibliotecas vendor, firmware e arquivos de ambiente.

| Entregável | Conteúdo |
| --- | --- |
| `DOCUMENTACAO_Xclipse_940_COMPLETA.pdf` | Plataforma, Device Tree, SGPU/DRM, GEM/BO/VA/IOMMU, CS/IB, scheduler, fences, Android, OpenCL, Vulkan, variantes e política de reprodução. |
| `MAPA_E_RAMIFICACAO_Xclipse_940.pdf` | Mapa de arquitetura, ramificação técnica, caminho operacional e legenda cromática. O vermelho identifica caminhos de investigação e possíveis falhas de integração. |
| `Xclipse-Open-Project-public-final.zip` | Os dois PDFs e os mapas PNG visuais. |

## Classificação

Os documentos diferenciam observação confirmada, confirmação por fonte, resultado parcial, indício, hipótese e resultado negativo específico. Um inicializador, uma enumeração ou um ioctl aceito não é apresentado como execução GPU sem sincronização e readback.

## Limites

Fontes Samsung são tratadas como referência de proveniência. O repositório não redistribui automaticamente código-fonte, firmware, bibliotecas vendor, executáveis, dumps, credenciais, identificadores ou logs brutos. XLIA e experimentos de NPU permanecem separados da documentação da GPU Xclipse.
