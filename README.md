# Xclipse Open Project

## Objetivo

O Xclipse Open Project é uma base técnica para documentação, coleta e mapeamento de GPUs Samsung Xclipse e dos aceleradores relacionados no SoC Exynos. O repositório organiza observações de hardware real, Device Tree, plataforma, kernel SGPU/DRM, memória, IOMMU, firmware, filas, sincronização, Android, bibliotecas de sistema e a separação entre GPU e NPU.

O nome interno do projeto é **XO940**. O hardware de referência é a **Samsung Xclipse 940** no **SM-S721B**, plataforma **s5e9945/erd9945**.

## Método de coleta

A fonte primária é um aparelho Samsung real, com coleta controlada e documentação da proveniência. O procedimento combina identidade do aparelho, build, kernel, SoC, GPU, revisão, firmware, Device Tree em runtime, comparação com fontes públicas, inventário de `/dev/dri`, sysfs, processos, namespaces, bibliotecas, traces do kernel e DRM, análise ELF e probes reversíveis.

A coleta também possui um ramo separado para NPU/NNAPI. Esse ramo registra seleção do dispositivo `enn`, compilação de grafos, execução, checksum, baseline de CPU, comportamento sustentado e testes com UID comum. Ele não é misturado à execução da GPU Xclipse.

## Política de evidências

Os documentos distinguem observação direta, confirmação por fonte, resultado parcial, indício estático, hipótese e resultado negativo específico. A existência de um arquivo chamado `test`, `probe`, `exec` ou `compute` não demonstra uma operação de GPU ou NPU. Uma afirmação de execução exige dispositivo-alvo, recurso visível para o acelerador, submissão, sincronização e resultado validado por checksum, readback ou apresentação.

## Estado atual resumido

A investigação confirmou o nó `sgpu@22200000`, o domínio de energia G3DCORE, regiões e recursos de plataforma, o render node `renderD128`, caminhos de GEM/BO, VM, VA, IOMMU, scheduler, IB, fences e o carregamento de componentes gráficos em processos Android suportados. Um probe DRM direto também identificou a camada reportada em quatro nós: `card0` e `renderD128` retornaram `amdgpu`, enquanto `card1` e `renderD129` retornaram `exynos-drmdpu`.

A coleta de 18/09 acrescentou a principal evidência do ramo NPU/NNAPI: um processo sem root obteve o serviço público `android.hardware.neuralnetworks.IDevice/enn`, compilou e executou um `SOFTMAX` com saída correta e diferença máxima de `0.000000`. A cadeia INT8 de quatro camadas `FULLY_CONNECTED` também executou no dispositivo `enn` com checksum correlacionado e tempo de 1,1148 ms por execução, contra 6,2407 ms na referência CPU. `BATCH_MATMUL` continua recusado pelo ENN no formato testado, e o acesso direto à interface vendor ou a `/dev/vertex10` não foi demonstrado para UID comum.

A identificação retornada pelo ioctl é uma propriedade da camada DRM observada. A correlação física completa continua sendo `nó → major/minor → sysfs → driver → platform device → sgpu@22200000`. A execução independente com buffer de saída, a semântica integral dos packets, a ISA e a cadeia completa de apresentação continuam em investigação.

## Ramificação técnica

```text
SoC Exynos / Android
├── GPU Xclipse / SGPU / DRM
│   └── renderD128 / GEM / VM / CS / IB / fences
├── NPU Exynos / NNAPI / ENN
│   └── grafos / compilação / checksum / baseline CPU
└── Reprodução e variantes
```

A **ramificação técnica** representa a estrutura física e lógica do SoC. O **mapa técnico** apresenta as relações entre camadas. O **mapa de investigação** marca em vermelho as áreas abertas, possíveis falhas de integração e fronteiras que precisam de testes adicionais.

## Segurança e proveniência

Código Samsung, firmware, bibliotecas vendor, dumps, logs crus e arquivos de ambiente não são redistribuídos automaticamente. Fontes públicas são tratadas como referência e passam por inventário de licença. O material público é sanitizado; XLIA e coletas de NPU não são misturados aos logs brutos da GPU.

## Referências

- [Política de validação](docs/validation-and-evidence.md)
- [Status técnico](STATUS.md)
- [Índice de documentação](docs/README.md)
- [Mapa público de evidências](source-analysis/xclipse-2026-09-06-evidence-map.md)
- [Mapa técnico da Device Tree](source-analysis/device-tree-technical-map.md)
- [NPU/ENN rootless](docs/30-npu-enn-rootless.md)
- [Relatório NPU/ENN de 18/09/2026](reports/npu-enn-rootless-2026-09-18.md)
- [Release pública](https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06)
