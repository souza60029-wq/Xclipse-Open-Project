# Análise de fontes Samsung

Os arquivos de fonte Samsung são tratados como pacotes de evidência e referência, não como dependência automaticamente redistribuível. A primeira operação é inventário, não compilação.

## Inventário obrigatório

Registrar SHA-256, contagem de arquivos, tamanhos comprimido e descomprimido, diretórios, duplicatas, arquivos gerados, avisos de copyright, identificadores SPDX, licenças e caminhos relacionados a GPU/DRM, UAPI, Device Tree, firmware, IOMMU, memória, filas, scheduling, reset, energia, Vulkan HAL, manifests, namespaces, SELinux e HWC.

Para cada arquivo relevante, registrar caminho, tipo, proveniência, símbolos, relação com SM-S721B/Xclipse 940 e classificação como interface pública, implementação de referência, artefato gerado ou detalhe proprietário.

## Limite de publicação

A privacidade do repositório não cria uma licença. Até que o inventário de licença esteja completo, código Samsung, firmware, bibliotecas vendor e binários permanecem como referência local. A documentação pública usa somente caminhos, hashes, inventários e descrições sanitizadas.

## Saídas esperadas

- inventário por membro de arquivo;
- inventário de licenças e avisos não resolvidos;
- caminhos de kernel para DRM, UAPI, VM, scheduler, reset e firmware;
- caminhos de integração Android para HAL, loader, manifest, namespace, SELinux e HWC;
- matriz de comparação das variantes S7210, S721J, S721Q, S721U e S721B;
- mapa de evidências com uma afirmação por resultado.

## Resultado de referência

O probe `X940-001c` identificou a camada reportada pelo DRM em quatro nós. Esse resultado precisa ser correlacionado com sysfs e com a Device Tree SGPU antes de ser interpretado como mapeamento físico completo.
