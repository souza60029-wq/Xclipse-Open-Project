# Xclipse Open Project

## Objetivo do repositório

O **Xclipse Open Project** existe para transformar observações de um aparelho real Samsung com Xclipse 940 em uma documentação técnica verificável. O projeto organiza a infraestrutura da GPU — Device Tree, plataforma, kernel SGPU/DRM, memória, IOMMU, firmware, filas, rings, Android e bibliotecas vendor — para que desenvolvedores possam estudar a arquitetura e avaliar, com segurança, a viabilidade de ferramentas e de um driver de espaço de usuário independente.

O nome interno do projeto é **XO940**. O hardware estudado é a **Samsung Xclipse 940** no **SM-S721B**, plataforma **s5e9945/erd9945**. XO940 não é o nome da GPU.

O repositório não afirma que RADV, Turnip ou AMDGPU possam ser usados diretamente. A finalidade é fornecer evidência suficiente para decidir o que pode ser reaproveitado, o que precisa ser adaptado e quais contratos ainda precisam ser descobertos.

## O que a página principal apresenta

A página principal não pretende listar aleatoriamente cada log, experimento ou arquivo intermediário. Ela apresenta somente:

1. **por que o projeto existe**;
2. **como os dados são coletados**;
3. **como os resultados são validados**;
4. **quais são os mapas técnicos para download**.

Os demais relatórios e inventários permanecem organizados nas pastas correspondentes para consulta técnica, sem transformar cada artefato em uma conclusão.

## Como os dados são coletados

A fonte primária é um **celular Samsung real**, identificado como SM-S721B, com acesso root controlado. A coleta combina:

- identidade do aparelho, build, kernel, SoC, GPU, revisão e firmware;
- leitura da Device Tree em runtime e comparação com o código-fonte Samsung fornecido;
- inventário de `/dev/dri`, sysfs, módulos, processos, namespaces e bibliotecas;
- observação de processos Android suportados, especialmente SurfaceFlinger, RenderThread, Gralloc e serviços que usam a GPU;
- traces do kernel e do DRM, incluindo memória, VM, scheduler, IB, ring, context, seqno e sincronização;
- análise estática de fontes, símbolos, strings e metadados ELF, sem tratar presença de arquivo como prova de execução;
- probes reversíveis, com registro do comando, ambiente, saída bruta e estado de recuperação.

Root permite observar recursos que um aplicativo comum não consegue acessar, mas **root não transforma Termux em membro do namespace vendor**, não libera automaticamente o ICD Samsung e não prova que uma operação de GPU foi executada.

Arquivos brutos, dumps, bibliotecas vendor, firmware, scripts de captura e testes reais não são publicados automaticamente. A documentação pública contém derivados sanitizados e referências de proveniência.

## Política de validação baseada em evidências

Cada afirmação precisa indicar o que foi observado, em qual aparelho/revisão, por qual método e qual limite permanece. A classificação usada pelo projeto é:

| Classe | Significado |
| --- | --- |
| **Confirmado** | Observado diretamente e reproduzido, ou sustentado por fonte verificável. |
| **Confirmado por fonte** | Caminho, interface ou comportamento localizado no código-fonte ou documentação. |
| **Parcial** | Parte do contrato foi observada, mas falta uma operação fim a fim. |
| **Indício** | Símbolo, string, export ou caminho sem prova de execução controlada. |
| **Hipótese** | Possibilidade de trabalho que ainda exige experimento. |
| **Negativo específico** | Uma abordagem determinada falhou; isso não invalida todas as alternativas. |

Um arquivo chamado `test`, `probe`, `exec` ou `compute` não é automaticamente um teste bem-sucedido. Para declarar execução real, é preciso demonstrar dispositivo-alvo, recurso visível para a GPU, submissão, sincronização e resultado validado por readback ou apresentação. Inicialização, enumeração, criação de pipeline, export ELF ou existência de ICD não substituem essa prova.

## Downloads técnicos principais

A release pública contém somente três entregáveis: a documentação completa em PDF, o PDF visual com mapa, ramificação e mapa de falhas, e um ZIP com esses dois PDFs e os PNGs visuais. As prioridades internas, fontes de teste, código-fonte e logs crus não são ativos de download.

- [Documentação completa da Xclipse 940](public/DOCUMENTACAO_Xclipse_940_COMPLETA.pdf)
- [Mapa, ramificação e falhas](public/MAPA_E_RAMIFICACAO_Xclipse_940.pdf)
- [Release pública com os três entregáveis](https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06)

## Estado atual resumido

Já foram observados o dispositivo SGPU, o render node `renderD128`, o caminho de memória/VM, o carregamento vendor em processo Android suportado e atividade de submissão GFX no caminho existente. O pacote Quasar acrescenta um probe DRM direto que identificou `card0`/`renderD128` como `amdgpu` e `card1`/`renderD129` como `exynos-drmdpu`; esse nome DRM ainda não prova compatibilidade AMDGPU ou RADV. O cliente independente do XO940 obteve aceitação de um CS específico, mas execução GPU própria, fence e readback continuam sem prova no conjunto XO940 público.

A sequência de trabalho é deliberadamente conservadora:

```text
Device Tree/plataforma
        ↓
DRM/UAPI + BO/VM/IOMMU
        ↓
context + IB + ring + scheduler
        ↓
fence + readback
        ↓
compute controlado
        ↓
backend Vulkan/Mesa experimental
```

## Quasar e reprodução entre aparelhos Xclipse

A branch [`quasar`](https://github.com/souza60029-wq/Xclipse-Open-Project/tree/quasar) preserva o workspace bruto recebido para reprodução: fontes, scripts, ambientes, hashes, logs, experimentos e probes DRM. O guia [`quasar/REPRODUCING.md`](quasar/REPRODUCING.md) orienta a execução em uma área com permissão de execução e o arquivamento posterior em armazenamento compartilhado.

O experimento `X940-001c` observou diretamente `DRM_IOCTL_VERSION` em quatro nós: `card0` e `renderD128` reportaram `amdgpu`, enquanto `card1` e `renderD129` reportaram `exynos-drmdpu`. A interpretação correta é uma identificação da camada DRM reportada no aparelho, não uma prova de GPU AMD física, ISA AMD ou compatibilidade direta com RADV.

O Quasar também documentou que o armazenamento compartilhado Android pode usar `noexec`; portanto, probes devem ser compilados e executados em uma área de trabalho executável. Esse resultado é operacional e evita classificar `Permission denied` como falha do KMD.

## Proveniência e segurança

O repositório é privado e não concede permissão para redistribuir código Samsung, firmware ou bibliotecas vendor. O material público é sanitizado e deve ser interpretado junto com os limites registrados nos relatórios.

- [Política completa de validação](docs/validation-and-evidence.md)
- [Status técnico](STATUS.md)
- [Índice de artefatos](artifacts/README.md)
- [Estudo detalhado de resultados](reports/estudo-detalhado-xclipse-940-2026-09-06.md)
- [Mapa público de evidências](source-analysis/xclipse-2026-09-06-evidence-map.md)
