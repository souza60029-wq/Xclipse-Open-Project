# Xclipse Open Project

## Objetivo

O Xclipse Open Project é uma base técnica para documentação, coleta reproduzível e mapeamento de GPUs Samsung Xclipse. O repositório organiza observações de hardware real, Device Tree, plataforma, kernel SGPU/DRM, memória, IOMMU, firmware, filas, sincronização, Android e bibliotecas de sistema.

O nome interno do projeto é **XO940**. O hardware de referência é a **Samsung Xclipse 940** no **SM-S721B**, plataforma **s5e9945/erd9945**. XO940 identifica o projeto e não a GPU.

## Método de coleta

A fonte primária é um aparelho Samsung real, com coleta controlada e documentação da proveniência. O procedimento combina identidade do aparelho, build, kernel, SoC, GPU, revisão, firmware, Device Tree em runtime, comparação com fontes públicas, inventário de `/dev/dri`, sysfs, processos, namespaces, bibliotecas, traces do kernel e DRM, análise ELF e probes reversíveis.

Root amplia a observabilidade, mas não remove automaticamente namespaces, permissões, SELinux ou restrições do Android. Cada coleta deve registrar comando, ambiente, saída bruta, timestamp, resultado, recuperação e SHA-256.

## Política de evidências

Os documentos distinguem observação direta, confirmação por fonte, resultado parcial, indício estático, hipótese e resultado negativo específico. A existência de um arquivo chamado `test`, `probe`, `exec` ou `compute` não demonstra uma operação de GPU. Uma afirmação de execução exige dispositivo-alvo, recurso visível para a GPU, submissão, sincronização e readback ou apresentação validada.

Logs brutos podem conter identificadores, caminhos privados, endereços, memória de processo, eventos de telefonia, bateria e estado pessoal do aparelho. Eles permanecem fora da release. O material público contém derivados técnicos sanitizados, mapas, tabelas, hashes e referências de proveniência.

## Estado atual resumido

A investigação confirmou o nó `sgpu@22200000`, o domínio de energia G3DCORE, regiões e recursos de plataforma, o render node `renderD128`, caminhos de GEM/BO, VM, VA, IOMMU, scheduler, IB, fences e o carregamento de componentes gráficos em processos Android suportados. Um probe DRM direto também identificou a camada reportada em quatro nós: `card0` e `renderD128` retornaram `amdgpu`, enquanto `card1` e `renderD129` retornaram `exynos-drmdpu`.

A identificação retornada pelo ioctl é uma propriedade da camada DRM observada. A correlação física completa continua sendo `nó → major/minor → sysfs → driver → platform device → sgpu@22200000`. A execução independente com buffer de saída, a semântica integral dos packets, a ISA e a cadeia completa de apresentação continuam em investigação.

A coleta mais recente também ampliou a base de comparação com variantes de Device Tree Samsung e trouxe material separado para XLIA e para subsistemas Exynos não pertencentes ao escopo principal. Esses conjuntos não são misturados à documentação da GPU.

## Sequência técnica

```text
Device Tree e plataforma
        ↓
DRM/SGPU UAPI + GEM/BO/VM/IOMMU
        ↓
context + IB + rings + scheduler
        ↓
fences + retire + readback
        ↓
compute e graphics controlados
        ↓
loader Android + ICD + WSI
        ↓
mapa de variantes e reprodução
```

## Organização técnica

A **ramificação técnica** representa a estrutura física e lógica da GPU: Device Tree, kernel, DRM, memória, energia, IOMMU, Android e caminhos de execução. O **mapa técnico** apresenta as relações entre camadas. O **mapa de investigação** marca em vermelho as áreas abertas, possíveis falhas de integração e fronteiras que precisam de testes adicionais.

## Segurança e proveniência

Código Samsung, firmware, bibliotecas vendor, dumps, logs crus e arquivos de ambiente não são redistribuídos automaticamente. Fontes públicas são tratadas como referência e passam por inventário de licença. O repositório permanece privado e a release contém somente documentação e mapas sanitizados.

## Referências

- [Política de validação](docs/validation-and-evidence.md)
- [Status técnico](STATUS.md)
- [Índice de documentação](docs/README.md)
- [Mapa público de evidências](source-analysis/xclipse-2026-09-06-evidence-map.md)
- [Mapa técnico da Device Tree](source-analysis/device-tree-technical-map.md)
- [Release pública](https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06)
