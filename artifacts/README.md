# Plano técnico em PDF

Este diretório contém duas versões do plano atualizado do projeto **Xclipse Open 940 — XO940**.

| Arquivo | Conteúdo |
| --- | --- |
| `PLANO_Xclipse_Open_940.pdf` | Plano técnico principal, sem a ramificação. Contém a documentação dos caminhos do chip, estado observado, testes, critérios e a abordagem 5 dois meios. |
| `RAMIFICACAO_Xclipse_Open_940.pdf` | PDF exclusivo da ramificação técnica. Contém somente a estrutura observada/mapeada do Xclipse: plataforma, kernel, GPU, firmware, DRM, Vulkan, OpenCL, processos Android, compiler e caminhos relacionados. Não é a árvore do repositório. |

O plano principal incorpora o estado observado no pacote fornecido, a separação entre probes e testes reais e a abordagem **5 dois meios**, com cinco descobertas essenciais e cinco descobertas úteis de segunda prioridade. A ramificação é deliberadamente separada e não repete o plano técnico do projeto.

Os PDFs foram compilados com Typst em modo estrito e passaram pela verificação determinística de assinatura, parseabilidade, texto, fontes e ausência de placeholders.

A ramificação contém caminhos observados no aparelho, caminhos localizados na fonte Samsung, interfaces vendor e relações técnicas inferidas. Um caminho inferido ou localizado não significa que sua implementação esteja aberta, carregada na revisão testada ou validada em runtime.
