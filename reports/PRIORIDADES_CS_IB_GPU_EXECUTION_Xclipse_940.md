# Novas prioridades de investigação da Xclipse 940

## Command submission, IB, rings, execução GPU e conclusão por fence

**Projeto:** XO940  
**Hardware:** Samsung Xclipse 940 — SM-S721B / s5e9945  
**Data:** 2026-09-06  
**Autor:** Manus AI  
**Classificação:** documentação pública derivada; não contém logs crus, scripts ou binários proprietários

## Resumo executivo

A investigação já confirmou atividade real de command submission gráfica no caminho vendor da Xclipse 940. Os traces P4.5, P4.6 e P4.12 registram `amdgpu_cs_ioctl`, execução pelo scheduler, `amdgpu_ib_schedule`, `sched_job`, `context`, `seqno`, `gfx_0.0.0` e `num_ibs=3`. Essa evidência é significativamente mais forte do que a simples presença de bibliotecas, símbolos ou inicializadores.

Entretanto, a cadeia completa que interessa ao desenvolvimento independente ainda não foi fechada. Continuam faltando, em uma captura correlacionada e controlada, o conteúdo bruto do CS/chunks, a identificação explícita de `AMDGPU_CHUNK_ID_IB`, os campos de VA/tamanho/alinhamento do IB, a `BO_LIST` do mesmo CS, a consulta `HW_IP_INFO`, a correlação completa entre out-fence/syncobj e conclusão, além de uma campanha sistemática de faults, hangs, resets, recovery e `dmesg` pareado.

A prioridade desta etapa é, portanto, observar o contrato operacional:

```text
userspace → CS ioctl → KMD → IB/ring → GPU → fence/completion
```

O documento não declara compatibilidade com RADV, Turnip ou AMDGPU upstream. Ele organiza o que já foi visto e define as lacunas que precisam ser fechadas.

## 1. Regra de classificação

| Estado | Significado |
| --- | --- |
| **Confirmado** | Observado diretamente em runtime ou em trace suficiente para a afirmação limitada. |
| **Parcial** | Parte da operação foi observada, mas falta correlação ou uma etapa essencial. |
| **Indireto** | Há um sinal relacionado, mas não o objeto ou estrutura solicitada. |
| **Não demonstrado** | A coleta existente não comprova a operação. |
| **Hipótese** | Possibilidade de implementação que ainda exige experimento. |

Um job agendado não é automaticamente uma execução independente validada. Para afirmar execução controlada, é necessário demonstrar o dispositivo-alvo, o recurso visível para a GPU, a submissão, a sincronização e um resultado independente, como readback ou apresentação.

## 2. O que já foi observado

A evidência existente sustenta a seguinte cadeia no caminho vendor:

```text
RenderThread / processo Android suportado
    ↓
amdgpu_cs_ioctl
    ↓
sched_job + context + seqno
    ↓
amdgpu_ib_schedule(num_ibs=3)
    ↓
gfx_0.0.0
    ↓
DRM GPU scheduler + dependências SGPU
    ↓
eventos relacionados a fence / retire / gpu_work_period
```

P4.5 relaciona `sgpu_pio_map_queue`, `amdgpu_vm_bo_cs`, `amdgpu_vm_flush` e o envio do job. P4.6 relaciona a atividade de VM com scheduler, IB e dependências SGPU. P4.12 contém uma janela com 13 chamadas de `amdgpu_cs_ioctl`, 13 execuções de scheduler e 13 chamadas de `amdgpu_ib_schedule`, todas associadas ao timeline/ring `gfx_0.0.0` e a `num_ibs=3`.

Essa é uma confirmação de atividade de CS/KMD/GFX no caminho observado. Ela não equivale à prova de um cliente próprio nem à prova de que o conteúdo dos IBs foi interpretado com sucesso e produziu um resultado conhecido.

## 3. Dez novas prioridades

| Nº | Prioridade | Estado atual | O que precisa ser fechado |
| ---: | --- | --- | --- |
| 1 | `drm_amdgpu_cs` / command submission | **Confirmado parcialmente** | Preservar e interpretar a captura do CS bruto, seus argumentos, chunks e associação com o job. |
| 2 | `AMDGPU_CHUNK_ID_IB` | **Indireto** | Extrair a estrutura do chunk IB e associá-la ao `num_ibs=3` observado. |
| 3 | Alinhamento, tamanho e VA do IB | **Não demonstrado** | Correlacionar VA, size e alignment de cada IB com o CS, BO e VM correspondente. |
| 4 | Criação, query e estado de context | **Parcial** | Capturar a criação, consulta, estado, prioridade e destruição do contexto usado no CS. |
| 5 | `BO_LIST` | **Parcial** | Identificar a lista de BOs pertencente ao mesmo CS e relacioná-la aos mapeamentos de VM. |
| 6 | Rings compute/GFX e `HW_IP_INFO` | **Parcial** | Fechar as informações de hardware disponíveis e separar GFX de compute com evidência de execução. |
| 7 | Fences, out-fences e syncobj | **Parcial** | Correlacionar submit, dependências, out-fence/syncobj, sinalização e retire do mesmo job. |
| 8 | Faults, hangs, resets e recovery | **Não capturado sistematicamente** | Fazer uma campanha controlada, não destrutiva quando possível, registrando sintoma, kernel log, reset e retorno do dispositivo. |
| 9 | `dmesg` durante CS | **Não correlacionado** | Preservar log do kernel com timestamp e pareá-lo com uma tentativa de CS identificada por contexto/seqno. |
| 10 | Command processor, ring submission e execução GPU | **Parcial forte** | Demonstrar conteúdo ou efeito do trabalho no ring/command processor e validar resultado por fence/readback. |

## 4. Detalhamento das prioridades

### 4.1 Command submission e `drm_amdgpu_cs`

A presença de `amdgpu_cs_ioctl` está confirmada nos traces. A documentação ainda não contém uma captura pública do ioctl bruto nem afirma que a ABI observada seja diretamente compatível com a ABI upstream. O próximo resultado relevante é uma associação inequívoca entre entrada de userspace, argumentos/chunks, `sched_job`, contexto, sequência e ring.

### 4.2 `AMDGPU_CHUNK_ID_IB`

`num_ibs=3` e `amdgpu_ib_schedule` demonstram que o caminho observado processou uma submissão com múltiplos IBs. Isso não revela, por si só, a estrutura `AMDGPU_CHUNK_ID_IB`, os ponteiros de userspace, os endereços GPU ou os tamanhos individuais. A prioridade é transformar o indício de múltiplos IBs em uma descrição verificável de chunks.

### 4.3 VA, tamanho e alinhamento do IB

A coleta já mostra atividade de VM, PTE, PDE e flush. Esses eventos provam que o caminho gerencia memória virtual durante atividade gráfica, mas não vinculam cada campo de um IB específico. A saída desejada é uma relação por job entre VA do IB, tamanho, alinhamento, BO de origem, mapeamento e sequência de VM.

### 4.4 Contexto de execução

`context` e `seqno` aparecem durante a submissão. A criação, query, prioridade, estado e destruição completos do contexto ainda não foram documentados para o mesmo workload. Essa lacuna é importante porque jobs simultâneos de RenderThread, compositor, câmera e outros serviços podem produzir atribuição incorreta.

### 4.5 `BO_LIST`

A criação e movimentação de BOs, bem como a atividade de VM, estão documentadas. O que falta é a lista de BOs pertencente ao CS específico. A correlação deve separar BO de comando, BO de dados, BO de page table e BOs auxiliares, sem confundir inventário geral de memória com a lista efetivamente referenciada pelo job.

### 4.6 Rings e `HW_IP_INFO`

O ring `gfx_0.0.0` está confirmado. A identidade de GFX e COMPUTE também foi inventariada. Ainda falta fechar a resposta equivalente a `HW_IP_INFO` e demonstrar, separadamente, um caminho compute controlado. A existência de nomes de ring não prova que cada tipo de IP esteja utilizável por um cliente independente.

### 4.7 Fences, out-fences e syncobjs

Há evidência de dependências SGPU, eventos de scheduler e sinais relacionados ao ciclo de vida de jobs. Contudo, a documentação não deve afirmar “fence concluída” apenas porque o job foi agendado. A prova necessária é a mesma identificação de job atravessando submit, dependência, out-fence ou syncobj, sinalização e retire, com um resultado observável.

### 4.8 Faults, hangs, resets e recovery

A documentação identifica timeout e recovery como etapas necessárias, mas não possui uma campanha específica que provoque ou observe sistematicamente faults, hangs, resets e recuperação. Essa prioridade deve ser tratada com controles de segurança, limites de tempo, preservação de logs e plano de retorno do dispositivo. Não se deve provocar falhas destrutivas sem procedimento apropriado.

### 4.9 Kernel log durante CS

Há inventários de kernel e traces de GPU, mas não uma captura temporalmente pareada entre tentativa de CS e `dmesg`. O valor desta prioridade está em distinguir erro de plataforma, MMU/IOMMU, firmware, scheduler, timeout e recuperação. O log deve manter timestamp, identidade do processo e referência ao contexto/seqno quando disponíveis.

### 4.10 Command processor e execução GPU

A combinação de scheduler, jobs processados, `amdgpu_ib_schedule`, `gfx_0.0.0` e `gpu_work_period` constitui evidência forte de atividade do caminho gráfico. A lacuna é o conteúdo do IB/ring e o efeito validado de um workload conhecido. A confirmação final deve ser feita com uma assinatura de saída e uma cadeia de sincronização que não dependa apenas de nomes de tracepoint.

## 5. Matriz de fechamento da cadeia

| Camada | Já existe na evidência | Falta para fechar |
| --- | --- | --- |
| Userspace | RenderThread e processo Android suportado observados | Isolar cliente e workload controlados |
| CS ioctl | `amdgpu_cs_ioctl` observado | Captura bruta e argumentos/chunks |
| KMD | scheduler, `sched_job`, contexto e seqno observados | ABI completa e correlação por job |
| IB | `amdgpu_ib_schedule` e `num_ibs=3` observados | `CHUNK_ID_IB`, VA, size e alignment |
| BO/VM | BO, PTE/PDE, VM update e flush observados | `BO_LIST` do mesmo CS |
| Ring | `gfx_0.0.0` observado | Conteúdo/efeito do ring ou command processor |
| GPU | atividade GFX correlacionada | Workload próprio com resultado conhecido |
| Fence | dependências e eventos relacionados observados | out-fence/syncobj até sinalização e retire |
| Kernel log | inventário existente | `dmesg` pareado durante CS |
| Recovery | roteiro identificado | faults/hangs/resets/recovery documentados |

## 6. Conclusão operacional

A investigação não precisa repetir a coleta geral de GEM, CPU mmap, VA ou PRIME, exceto quando esses dados forem necessários para correlacionar um CS. A próxima etapa deve ser uma captura dirigida ao KMD/CS/IB, preservando logs crus localmente com timestamp e proveniência, enquanto o repositório público recebe apenas documentação sanitizada e derivada.

O estado atual pode ser resumido assim:

> **Já existe prova de atividade real de command submission GFX no caminho vendor. Ainda falta a prova estrutural e independente do contrato CS → IB → ring → GPU → fence/readback.**

## Referências

[1]: ../reports/estudo-detalhado-xclipse-940-2026-09-06.md "Estudo detalhado da Xclipse 940"
[2]: ../artifacts/MAPA_Xclipse_940_COMPLETO.md "Mapa completo da Xclipse 940"
[3]: ../docs/validation-and-evidence.md "Protocolo de validação e evidências"
[4]: ../source-analysis/xclipse-2026-09-06-evidence-map.md "Mapa público de evidências da coleta"

[1] [2] [3] [4]

> Este documento registra o estado da documentação existente. Ele não afirma que uma captura futura já foi realizada nem que um driver independente está funcional.
