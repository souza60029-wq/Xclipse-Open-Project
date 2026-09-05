# 5 prioridades + 5 descobertas rápidas — Xclipse Open Project

**Terminologia:** **Xclipse 940** é o hardware/GPU investigado. **XO940** é apenas o nome interno do projeto Xclipse Open Project.

O nome anterior “5 dois meios” foi mantido como referência histórica, mas a ordem de investigação foi substituída. As prioridades agora começam pela **Device Tree e pelo contrato de plataforma**, porque sem entender como o SGPU é ligado ao SoC, à energia, ao IOMMU, à memória e ao firmware, um teste de Vulkan ou de compute pode produzir uma falsa conclusão.

## O que é a Device Tree neste projeto

A Device Tree é a descrição declarativa, usada pelo kernel, de como o hardware está conectado e quais recursos ele possui. Ela registra, entre outros elementos, endereços de registradores, interrupções, power domains, clocks, DMA/coerência, compatibilidade do dispositivo, revisões e relações com controladores auxiliares.

No caso investigado, os arquivos mais relevantes incluem:

- `arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi`;
- `arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi`;
- o nó `/sgpu@22200000`;
- o compatível `samsung-sgpu,samsung-sgpu`;
- caminhos Samsung de IOMMU;
- heaps Samsung de DMA-BUF;
- integração com energia e DVFS.

A Device Tree não é, sozinha, o driver. Ela é o mapa de recursos que permite ao driver saber **onde o bloco está, quais interrupções usa, como recebe energia, como acessa memória e com quais revisões de hardware é compatível**.

## As 5 prioridades atuais

### 1. Fechar a Device Tree da Xclipse 940 e o contrato de plataforma

**Pergunta:** quais são exatamente os recursos de hardware fornecidos ao driver SGPU para a revisão do SM-S721B?

**Precisamos descobrir:**

- todas as regiões de registradores;
- interrupções e seus significados;
- power domain;
- clocks e OPP/DVFS;
- `dma-coherent` e propriedades DMA;
- vínculos com IOMMU;
- doorbell e regiões de debug;
- revisão `0x02600200` versus variantes EVT0;
- diferenças entre `s5e9945-sgpu_common.dtsi` e `s5e9945-sgpu_evt0.dtsi`;
- dependências de reset, PM runtime e AFM/IFPO.

**Por que é a prioridade número 1:** sem isso, qualquer driver pode acessar endereço errado, usar interrupção errada, assumir clock incorreto ou ignorar uma dependência de energia. O resultado pode ser hang, reset ou corrupção de memória.

**Evidência mínima de saída:** um diagrama `Device Tree → kernel SGPU → IOMMU/power/clock → DRM node`, com cada propriedade ligada a uma função ou subsistema do driver.

### 2. Fechar memória, IOMMU, DMA-BUF e segurança de buffers

**Pergunta:** como um BO sai da memória Android, passa pelo IOMMU e se torna acessível à GPU?

**Precisamos descobrir:**

- heaps usados por cada processo;
- relação entre GEM, TTM, DMA-BUF e IOMMU;
- flags de cache e coerência;
- VA range e permissões;
- alinhamento e page size;
- residency e eviction;
- buffers seguros/protegidos;
- page faults e recuperação;
- diferença entre CPU mapping e GPU access;
- cache flush/invalidate e sincronização.

**Por que é prioridade:** o probe atual provou criação de BO, mapeamento CPU e VA map/unmap, mas ainda não provou que a GPU acessou aquele BO. A maior fonte de risco em um primeiro driver móvel é confundir “mapeável pela CPU” com “corretamente visível para a GPU”.

**Evidência mínima de saída:** uma tabela de lifecycle de BO com handle, DMA-BUF, VA, permissões, cache, fence e resultado de fault/readback.

### 3. Documentar o caminho vendor Android suportado

**Pergunta:** como o Android de produção chega ao ICD Samsung e às bibliotecas SGPU?

**Precisamos descobrir:**

- processo responsável;
- linker namespace;
- manifest/HAL;
- dependências e SONAMEs;
- permissões dos nós DRM;
- relação com `SurfaceFlinger`;
- relação com `com.samsung.android.photoremasterservice:photoremasterservice`;
- diferenças entre Vulkan e OpenCL;
- bibliotecas de mapper/gralloc;
- SELinux domains e regras relevantes;
- quais interfaces são públicas, vendor-only ou privadas.

**Por que é prioridade:** já observamos que o `SurfaceFlinger` mapeia o ICD Samsung, enquanto o Termux encontra `llvmpipe`. Portanto, a primeira barreira não é necessariamente a GPU: pode ser o ambiente de carregamento.

**Evidência mínima de saída:** um mapa reproduzível `processo → namespace → biblioteca → DRM FD → serviço/HAL`, sem substituir arquivos de `/system` ou `/vendor`.

### 4. Reconstituir firmware, filas, reset e submission a partir de uma operação real

**Pergunta:** como o SGPU inicializa, recebe trabalho, sinaliza conclusão e se recupera de erro?

**Precisamos descobrir:**

- ordem de carregamento dos firmwares;
- GFX/COMPUTE/DMA IPs realmente ativos;
- rings e queues utilizados;
- IB/chunk format;
- doorbells;
- contexto e prioridade;
- fences, syncobjs e semáforos;
- reset state;
- timeout e recuperação;
- quais interfaces `sgpu_cs_*` são realmente chamadas.

**Por que é prioridade:** a existência de `sgpu_cs_submit` no binário não prova que conhecemos o pacote aceito pelo firmware. Um submit cego pode travar a GPU ou exigir recuperação que ainda não entendemos.

**Evidência mínima de saída:** trace de uma operação vendor real correlacionando FD, contexto, queue/ring, submission, fence, conclusão e eventual reset.

### 5. Capturar compute e shader reais pelo caminho suportado

**Pergunta:** conseguimos sair da análise de nomes e observar uma operação Xclipse 940 completa?

**Precisamos descobrir:**

- entrada controlada;
- kernel ou shader identificado;
- recurso/BO associado;
- dispatch ou draw real;
- fence/evento;
- saída conhecida;
- binary ou metadado produzido;
- relação entre SPIR-V/OpenCL e o backend vendor;
- estabilidade entre revisões.

**Por que é prioridade:** esse é o ponto que transforma infraestrutura documentada em comportamento comprovado. A operação deve ser observada primeiro pelo Photo Remaster ou por outro caminho suportado, antes de tentar um submit próprio.

**Evidência mínima de saída:** `input conhecido → operação vendor → execução Xclipse 940 → output conhecido`, com hashes, timestamps e classificação de cada artefato.

## As 5 descobertas mais fáceis e rápidas

Estas tarefas não substituem as cinco prioridades. Elas são escolhidas para gerar avanço rápido com baixo risco e pouca dependência de execução GPU.

### R1. Extrair e comparar todos os nós Device Tree SGPU

Comparar `s5e9945-sgpu_common.dtsi`, `s5e9945-sgpu_evt0.dtsi`, includes relacionados, `Kconfig` e `Makefile`. Gerar uma tabela com endereço, tamanho, interrupção, clock, power domain, compatível e revisão.

**Resultado esperado:** primeiro mapa técnico confiável da plataforma.

### R2. Inventariar o estado runtime já disponível

Consolidar em uma única tabela:

- `/dev/dri/card0`;
- `/dev/dri/renderD128`;
- sysfs do SGPU;
- driver platform;
- firmware;
- frequência atual e faixa disponível;
- módulos carregados;
- permissões;
- processos com FDs para `renderD128`.

**Resultado esperado:** eliminar coletas duplicadas e identificar rapidamente mudanças entre sessões.

### R3. Mapear bibliotecas, BuildIDs e dependências

Registrar tamanho, ELF class, arquitetura, BuildID, SONAME, NEEDED e caminho de cada biblioteca relacionada:

- `vulkan.samsung.so`;
- `libdrm_sgpu.so`;
- `libOpenCL.so`;
- `libSGPUOpenCL.so`;
- `libvulkan.so`;
- mapper/gralloc relacionado.

**Resultado esperado:** mapa ABI/vendor sem precisar executar comandos perigosos.

### R4. Indexar UAPI, Kconfig, Makefile e símbolos por subsistema

Gerar índices separados para:

- GEM/TTM;
- VM/IOMMU;
- CS/IB/rings;
- fences/syncobjs;
- firmware/reset;
- GFX/COMPUTE/SDMA;
- DVFS/debug.

Cada item deve apontar para o caminho fonte e ser marcado como **interface**, **implementação**, **configuração**, **log** ou **hipótese**.

**Resultado esperado:** reduzir o tempo necessário para encontrar a implementação de uma função observada no runtime.

### R5. Catalogar a análise estática do ICD com níveis de confiança

Organizar strings, referências AArch64, nomes de arquivos internos e mensagens de erro em três níveis:

1. presença textual;
2. referência em código/log;
3. comportamento confirmado em runtime.

Incluir `SPIR-V`, `SCEmitter`, `SCAsmEncoder`, `GetHwOpcode`, `XlateOpcode`, VOP e DPP, sem tratá-los automaticamente como ISA decodificada.

**Resultado esperado:** acelerar hipóteses do compiler sem transformar strings em falsas provas.

## Ordem operacional recomendada

A ordem mais segura agora é:

```text
R1 + R2 + R3 + R4 + R5
        ↓
P1 Device Tree e plataforma
        ↓
P2 memória/IOMMU/DMA-BUF
        ↓
P3 loader/vendor Android
        ↓
P4 firmware/queues/reset
        ↓
P5 compute/shader real
```

A antiga prioridade de “começar logo pelo dispatch” foi rebaixada. O dispatch continua sendo essencial, mas só depois de sabermos que o processo, o BO, o IOMMU, a fila, o firmware e a recuperação estão corretamente compreendidos.

## Critério contra falsos positivos

Os seguintes resultados continuam insuficientes isoladamente:

- `compute pipeline ok`;
- presença de `clEnqueueNDRangeKernel`;
- presença de `sgpu_cs_submit`;
- frequência SGPU mudando;
- biblioteca vendor carregada;
- string `GetHwOpcode`;
- pipeline criado;
- BO mapeado pela CPU;
- nome de teste ou diretório de teste.

Um resultado só entra como execução real quando existe contexto, ação observável, sincronização e validação independente da saída.

## Referências locais

- `docs/00-project-scope.md` — escopo e terminologia.
- `source-analysis/selected-paths.md` — caminhos Samsung já mapeados.
- `source-analysis/new-results-paths.md` — caminhos do novo pacote.
- `ROADMAP.md` — fases e critérios de saída.
- `STATUS.md` — ledger de evidências.
