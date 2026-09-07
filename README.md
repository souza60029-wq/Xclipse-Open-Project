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

### 1. Mapa completo Xclipse 940

O mapa completo funde a **ramificação visual**, a explicação técnica e os pontos de entrada para um futuro cliente DRM/SGPU ou port de driver. A imagem aparece no início do documento e cada ramo é explicado nas seções seguintes, incluindo Device Tree, kernel, BO/VM, IOMMU, GFX, Android, OpenCL, Vulkan e limites de evidência.

- [Mapa completo — visual + técnico + pontos de entrada](artifacts/MAPA_Xclipse_940_COMPLETO.md)
- [Imagem do mapa](artifacts/MAPA_Xclipse_940.png)

### 2. Documentos complementares

Os PDFs permanecem como versões de estudo e a fonte Mermaid continua disponível para edição, mas não são necessários para entender o mapa completo.

- [Ramificação técnica em PDF](artifacts/RAMIFICACAO_Xclipse_Open_940.pdf)
- [Estudo detalhado em PDF](artifacts/ESTUDO_DETALHADO_Xclipse_940_2026-09-06.pdf)
- [Novas prioridades: CS, IB, rings, fences e execução GPU](artifacts/PRIORIDADES_CS_IB_GPU_EXECUTION_Xclipse_940.pdf)
- [Fonte editável Mermaid](artifacts/MAPA_Xclipse_940.mmd)

### 3. Pacote de documentação pública

- [Release de documentação](https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06)
- [Pacote ZIP completo](artifacts/Xclipse-Open-Project-public-documentation-2026-09-06.zip)

## Estado atual resumido

Já foram observados o dispositivo SGPU, o render node `renderD128`, o caminho de memória/VM, o carregamento vendor em processo Android suportado e atividade de submissão GFX no caminho existente. Ainda não foi demonstrado um cliente independente com IB próprio, fence própria, readback controlado, compute externo ou driver Vulkan independente.

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

## Proveniência e segurança

O repositório é privado e não concede permissão para redistribuir código Samsung, firmware ou bibliotecas vendor. O material público é sanitizado e deve ser interpretado junto com os limites registrados nos relatórios.

- [Política completa de validação](docs/validation-and-evidence.md)
- [Status técnico](STATUS.md)
- [Índice de artefatos](artifacts/README.md)
- [Estudo detalhado de resultados](reports/estudo-detalhado-xclipse-940-2026-09-06.md)
- [Mapa público de evidências](source-analysis/xclipse-2026-09-06-evidence-map.md)
