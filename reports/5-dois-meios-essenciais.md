# 5 dois meios — 10 objetivos atuais da Xclipse 940

**Projeto interno:** XO940
**Hardware:** Samsung Xclipse 940 no SM-S721B
**Regra:** os cinco primeiros objetivos são bloqueadores técnicos. Os cinco seguintes são descobertas rápidas, de baixo risco e úteis para acelerar o trabalho.

## O que estamos tentando construir

O objetivo de longo prazo é descobrir se a Xclipse 940 pode receber um **driver de espaço de usuário independente**, com uma arquitetura comparável à de um driver Mesa/Vulkan ou a uma camada customizada que converse com o kernel SGPU existente. Isso não significa assumir que a Xclipse é uma AMDGPU de PC, nem que o caminho RADV possa ser copiado. A árvore Device Tree, a UAPI SGPU, os firmware, a VM, os rings e a ABI Android precisam ser tratados como contratos próprios.

A evidência atual mostra um caminho vendor real de submissão GFX, mas ainda não mostra que um cliente novo consiga controlar esse caminho e validar sua saída. Portanto, o projeto deve avançar da plataforma para o kernel, do kernel para memória/submissão, e só depois para Vulkan, shader e compiler.

## Os cinco objetivos mais importantes

### C1 — Fechar o contrato Device Tree → kernel SGPU → plataforma

Documentar completamente o nó `sgpu@22200000`, regiões `gpu`, `doorbell`, `debug`, `pwrctl`, `sysreg` e `htu`, IRQs `SGPU`/`GPU-AFM`, G3DCORE, clocks, DVFS, reset, AFM/IFPO, DMA-coherent e variantes EVT0.

**Saída:** mapa reproduzível ligando cada propriedade da Device Tree ao subsistema kernel que a consome. Sem essa saída, falhas de energia, clock ou reset podem ser confundidas com falhas do driver.

### C2 — Reproduzir o ciclo de vida de memória do cliente

Fechar o caminho BO/GEM/TTM/DMA-BUF → heap → IOMMU/SysMMU → VA → permissões → cache → flush → unmap. O objetivo é diferenciar “a CPU conseguiu mapear” de “a GPU conseguiu acessar corretamente”.

**Saída:** tabela controlada de alocação, mapeamento, sincronização, erro e limpeza, incluindo page size, alinhamento, residency e buffers protegidos quando aplicável.

### C3 — Isolar e correlacionar um job GFX controlado

Usar a evidência já observada de `amdgpu_cs_ioctl`, `amdgpu_sched_run_job`, `amdgpu_ib_schedule`, `gfx_0.0.0`, `context` e `seqno` para separar um workload conhecido das atividades do compositor, câmera, Chrome e RenderThread.

**Saída:** um job próprio com IB conhecido, contexto identificado, fence/seqno correlacionados e buffer de resultado validado. Agendamento de job sem resultado não basta.

### C4 — Definir a fronteira entre kernel aberto, ABI vendor e cliente independente

Mapear quais ioctls, structs, flags, handles, permissões, namespaces e bibliotecas são realmente necessários para um cliente novo. Separar a UAPI SGPU pública ou observável das interfaces internas e das dependências proprietárias.

**Saída:** uma tabela de contrato mínimo com origem, licença, estabilidade, processo consumidor e risco de reutilização. Esse é o ponto que decide se a rota deve aproveitar o kernel existente ou exigir uma adaptação adicional.

### C5 — Demonstrar o primeiro workload independente, começando por compute

Tentar um caminho mínimo e reversível: identificar dispositivo, abrir o caminho permitido, criar contexto, alocar entrada/saída, submeter uma operação simples e validar readback. OpenCL vendor pode servir como referência de comportamento, mas não deve ser tratado como ABI pública automaticamente.

**Saída:** `entrada conhecida → execução Xclipse 940 → saída conhecida`, ou um relatório preciso dizendo em qual camada o caminho foi bloqueado. P5.04, por si só, foi uma captura negativa e não cumpre este objetivo.

## As cinco descobertas mais fáceis e rápidas

### Q1 — Comparar automaticamente todas as variantes Device Tree SGPU

Gerar uma tabela entre `s5e9945-sgpu_common.dtsi`, `s5e9945-sgpu_evt0.dtsi` e includes relacionados, destacando diferenças de `reg`, IRQ, clocks, power-domain, IOMMU, DMA e revisão.

**Por que é rápida:** é trabalho de fonte e inventário, sem submeter GPU. **Resultado:** reduz o risco de usar a variante errada no primeiro driver.

### Q2 — Construir um inventário único do runtime SGPU

Consolidar nós DRM, sysfs, firmware, frequência, módulos, permissões, processos e FDs para `renderD128`, mantendo timestamps e identidade do dispositivo.

**Por que é rápida:** os dados já aparecem em R1/R2/P2. **Resultado:** elimina coletas duplicadas e mostra quais condições mudam entre sessões.

### Q3 — Mapear bibliotecas e namespaces da cadeia vendor

Catalogar caminho, arquitetura ELF, SONAME, NEEDED, BuildID, namespace e processo para `vulkan.samsung.so`, `libdrm_sgpu.so`, `libOpenCL.so`, `libSGPUOpenCL.so` e `libvulkan.so`.

**Por que é rápida:** é análise estática e de runtime, sem publicar os binários. **Resultado:** identifica a primeira barreira real para um cliente externo.

### Q4 — Indexar a UAPI e os símbolos por subsistema

Criar índices separados para GEM/BO, VM/IOMMU, CS/IB/ring, fences/syncobjs, firmware/reset, GFX/COMPUTE/SDMA e DVFS/debug, marcando cada símbolo como interface, implementação, configuração, log ou hipótese.

**Por que é rápida:** aproveita a fonte Samsung já inventariada. **Resultado:** acelera a localização das funções correspondentes aos eventos de runtime.

### Q5 — Isolar a assinatura de um job real no trace

Escolher uma janela P4.5/P4.6/P4.12 e seguir `sched_job`, `context`, `seqno`, PID, `num_ibs`, ring, VM flush e eventos de limpeza. Não é necessário ainda interpretar o IB.

**Por que é rápida:** os traces já contêm grande parte dos identificadores. **Resultado:** produz o primeiro modelo de causalidade sem fingir que houve readback.

## Ordem de execução

```text
Q1 + Q2 + Q3 + Q4 + Q5
          ↓
C1: Device Tree e plataforma
          ↓
C2: memória, IOMMU e VM
          ↓
C3: job GFX isolado e conclusão
          ↓
C4: contrato UAPI/ABI e ponte Android
          ↓
C5: primeiro workload independente
          ↓
shader / ISA / compiler / Vulkan
```

## Critério contra falsos positivos

Um nome contendo `test`, `probe`, `trace`, `compute` ou `submit` não classifica o arquivo como teste bem-sucedido. Inicializador, consulta de capacidade, descoberta de tracepoint, criação de pipeline sem dispatch e trace sem resultado são evidências parciais. Só promovemos uma operação quando sua execução, contexto e saída são observáveis.

## Decisão técnica atual

A Xclipse 940 não deve ser tratada como uma simples GPU de PC transplantada para um celular. O caminho observado combina elementos familiares do ecossistema AMD/AMDGPU — BO, VM, scheduler, IB, rings e nomenclatura de traces — com integração Samsung específica de Device Tree, power-domain, IOMMU, firmware, Android, namespaces e bibliotecas vendor. Isso torna uma arquitetura de driver independente plausível como investigação, mas impede copiar diretamente um driver existente sem validar cada contrato.

Os artefatos crus, testes, scripts de captura, bibliotecas e binários permanecem fora do GitHub público. Este documento publica apenas objetivos, critérios e interpretação técnica.
