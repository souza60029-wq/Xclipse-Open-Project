# Plano técnico em PDF

Este diretório contém duas versões do plano atualizado do projeto **Xclipse Open Project — XO940**.

| Arquivo | Conteúdo |
| --- | --- |
| `PLANO_Xclipse_Open_940.pdf` | Plano técnico principal, sem a ramificação. Contém a documentação dos caminhos do chip, estado observado, testes, critérios e a abordagem 5 dois meios. |
| `RAMIFICACAO_Xclipse_Open_940.pdf` | PDF exclusivo da ramificação técnica. Contém somente a estrutura observada/mapeada do Xclipse: plataforma, kernel, GPU, firmware, DRM, Vulkan, OpenCL, processos Android, compiler e caminhos relacionados. Não é a árvore do repositório. |
| `ESTUDO_DETALHADO_Xclipse_940_2026-09-06.pdf` | Estudo público detalhado da nova coleta: Device Tree, IOMMU, VM/BO, submissão GFX, scheduler/IB, Android vendor, OpenCL, bloqueios e rotas de investigação. Inclui a ramificação técnica e o mapa de evidências. |

O plano principal incorpora o estado observado no pacote fornecido, a separação entre probes e testes reais e a abordagem **5 dois meios**, com cinco descobertas essenciais e cinco descobertas úteis de segunda prioridade. O estudo detalhado registra a atualização de 2026-09-06. A ramificação é deliberadamente separada e representa apenas a estrutura técnica do hardware, não o plano do projeto.

Os PDFs foram compilados com Typst em modo estrito e passaram pela verificação determinística de assinatura, parseabilidade, texto, fontes e ausência de placeholders.

A ramificação contém caminhos observados no aparelho, caminhos localizados na fonte Samsung, interfaces vendor e relações técnicas inferidas. Um caminho inferido ou localizado não significa que sua implementação esteja aberta, carregada na revisão testada ou validada em runtime.
