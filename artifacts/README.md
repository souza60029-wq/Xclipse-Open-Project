# Artefatos públicos do XO940

A distribuição pública foi consolidada para reduzir duplicação e evitar que documentos de trabalho sejam confundidos com resultados finais. A release contém somente três ativos: dois PDFs de estudo e um ZIP que reúne os PDFs e as imagens dos mapas.

| Entregável | Conteúdo |
| --- | --- |
| `DOCUMENTACAO_Xclipse_940_COMPLETA.pdf` | Documentação técnica consolidada: plataforma, Device Tree, SGPU/DRM, GEM/BO/VA/IOMMU, CS/IB, scheduler, fences, Android, OpenCL, Vulkan, compiler/ISA e resultados técnicos dos experimentos de cliente próprio. |
| `MAPA_E_RAMIFICACAO_Xclipse_940.pdf` | Mapa visual da infraestrutura, ramificação técnica, legenda cromática e mapa visual de possíveis falhas/brechas de portabilidade. |
| `Xclipse-Open-Project-public-final.zip` | Pacote com os dois PDFs e os dois PNGs visuais. |

As prioridades internas não são ativos públicos. Fontes C, código-fonte, executáveis de teste, bibliotecas vendor, firmware, logs crus, dumps de memória e `dmesg` completo também não são ativos de download.

Os PDFs usam linguagem técnica e distinguem explicitamente fato observado, confirmação por fonte, resultado parcial, indício, hipótese e falha específica. A aceitação de um ioctl de CS não é descrita como execução GPU sem fence e readback independentes.
