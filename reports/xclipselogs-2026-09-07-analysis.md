# Análise técnica do pacote `xclipselogs.zip`

**Data da análise:** 2026-09-07  
**Hardware-alvo:** Samsung Xclipse 940 — SM-S721B / s5e9945  
**Pacote recebido:** `xclipselogs.zip`  
**SHA-256 do pacote:** `4f97af4b73b5311b128b189d659e3829baf8cdee457045548638e5d15d6c2284`  
**Política:** logs crus, fontes C, binários de teste e dumps permanecem fora do GitHub.

## Resumo

Este pacote é uma nova rodada de experimentos de baixo nível sobre o endpoint DRM `/dev/dri/renderD128`. Ele contém fontes C de clientes ARM64, saídas de compilação, resultados de execução, hashes, testes de abertura do render node, GEM/CPU mmap, VA mapping, BO_LIST, criação de contexto, tentativas de command submission e capturas de `dmesg` antes/depois.

O achado novo mais importante é que um cliente próprio conseguiu obter **`CS_IOCTL=ACCEPTED`** em `A1.5_REAL_CS_20260907_161914`, usando um BO de 64 KiB, VA `0x4000000000`, um chunk IB de 4 bytes, BO list e contexto recém-criados. Isso prova aceitação do ioctl de command submission pelo KMD para aquela estrutura de entrada.

Essa aceitação ainda não prova execução GPU, interpretação do packet, sinalização de fence, conclusão, escrita no BO ou readback. A maioria das variantes próximas foi rejeitada com `errno=14 (Bad address)`, e os testes de espera posteriores não fecharam uma fence própria.

## O que são esses logs

| Família | Conteúdo | Classificação |
| --- | --- | --- |
| `XCLIPSE_STRIKE` | Testes incrementais do cliente DRM próprio: abertura do render node, GEM, mmap, VA, BO list, contexto e CS | experimentos de bring-up e validação de ABI; não são logs de uma aplicação gráfica comum |
| `A1.5_*` | Tentativas focadas de montar uma entrada `DRM_IOCTL_AMDGPU_CS` com chunk IB, BO list, contexto e VA | captura de contrato de submit; inclui rejeições e uma aceitação de ioctl |
| `A1.6_*` | Variações de CS com espera e captura de `dmesg` antes/depois | correlação de conclusão e kernel log; a variante registrada foi rejeitada ou não fechou execução |
| `A1.1_sgpu_desgruncher` | Experimento de inspeção/descoberta de interfaces | investigação auxiliar; não é prova de execução |
| `A1.2_shader_execution` | tentativa orientada a shader/execução | deve ser lida como roteiro e resultado experimental, não como prova automática de shader executado |
| `A1.4_ABI_HUNTER*` | procura e checagem de estruturas/ABI | análise de contrato, não execução GPU |
| `littlehunter` | estado, runs e dados auxiliares do processo de investigação | metadados de ferramenta e histórico, não evidência direta de hardware |

## Resultados confirmados nesta rodada

### 1. Cliente próprio acessa o render node

O cliente ARM64 abriu `/dev/dri/renderD128` com sucesso, consultou a versão DRM e identificou o driver como `amdgpu`, versão `3.42.0`. As capacidades observadas incluem `SYNCOBJ` e `SYNCOBJ_TIMELINE`.

### 2. GEM/BO e CPU mmap

O cliente criou um BO GTT de 64 KiB com alinhamento de 4096 bytes. Variantes posteriores também realizaram `GEM_MMAP` e mapeamento CPU. Isso confirma o caminho básico de objeto e acesso CPU do cliente próprio.

### 3. VA mapping executável

`STRIKE03` mapeou e desmapeou VA `0x4000000000` com sucesso. `STRIKE05` e `STRIKE05_1` repetiram o caminho com `VA_MAP_EXECUTABLE=SUCCESS`, seguido de unmap e fechamento do GEM.

### 4. BO_LIST próprio

`STRIKE04` criou e destruiu uma BO list com sucesso. `STRIKE05` também criou e destruiu uma lista durante a preparação de um IB. Isso eleva a evidência de BO_LIST de “apenas documentada na UAPI” para “operação própria aceita pelo ioctl de BO list”. Ainda não prova que uma lista correta foi consumida por um CS executado.

### 5. Contexto próprio

`STRIKE06` e as variantes A1.5/A1.6 conseguiram alocar e liberar contexto. No caso aceito de A1.5, o contexto retornou `ctx_id=1` e prioridade `0`.

### 6. Primeiro CS próprio aceito pelo ioctl

Em `A1.5_REAL_CS_20260907_161914`, a entrada registrada foi:

```text
DEVICE=/dev/dri/renderD128
BO handle=0x1 size=0x10000
IB va=0x4000000000 bytes=4
VA_MAP ioctl_ret=0 flags=0xe
BO_LIST operation=1 list_handle=0x1
CTX op=1 ctx_id=1 priority=0
chunk_id=0x1 length_dw=8
ib_bytes=4 ip_type=0 ip_instance=0 ring=0 flags=0x0
num_chunks=1
CS ioctl_ret=0 errno=0
CS_IOCTL=ACCEPTED
```

O resultado permitido é: **o KMD aceitou o ioctl de CS para essa entrada de userspace**. A documentação não deve promover esse resultado a “GPU executou” porque o IB tinha apenas 4 bytes (`0xc0001000`) e não houve readback, fence própria correlacionada ou prova independente de efeito no BO.

## Rejeições que também são evidência

As variantes `STRIKE06`, `STRIKE07`, `STRIKE08`, `STRIKE09` e várias tentativas A1.5 foram rejeitadas. Os erros registrados incluem `EINVAL` em uma tentativa de CS e `EFAULT`/`Bad address` em várias estruturas com ponteiros de chunks/IB.

Essas rejeições são úteis para depurar o contrato de ponteiros, layout e validação do ioctl. Elas não provam que o render node bloqueia todo CS próprio. A existência de uma aceitação em A1.5 demonstra que pelo menos uma forma de entrada passou da validação inicial do ioctl.

## O que não foi demonstrado

| Pergunta | Estado após este pacote |
| --- | --- |
| Cliente próprio abre `/dev/dri/renderD128`? | **Confirmado** |
| Cliente próprio cria GEM/BO? | **Confirmado** |
| Cliente próprio faz CPU mmap? | **Confirmado** |
| Cliente próprio faz VA map/unmap? | **Confirmado** |
| Cliente próprio cria BO_LIST? | **Confirmado** |
| Cliente próprio cria contexto? | **Confirmado** |
| Cliente próprio tem um CS aceito pelo ioctl? | **Confirmado para uma entrada específica** |
| `AMDGPU_CHUNK_ID_IB` é aceito na entrada? | **Indício forte; chunk id `0x1` foi aceito junto com o CS** |
| IB foi executado pelo command processor? | **Não demonstrado** |
| Fence/out-fence/syncobj própria sinalizou? | **Não demonstrado** |
| GPU escreveu no BO? | **Não demonstrado** |
| CPU readback validou resultado? | **Não demonstrado** |
| Fault/hang/reset/recovery ocorreu ou foi evitado? | **Não concluído** |
| `dmesg` foi correlacionado ao CS aceito? | **Não demonstrado** |

## Proveniência e privacidade

O pacote contém fontes C, executáveis, logs de compilação, saídas de execução, hashes e dumps de `dmesg`. Esses artefatos permanecem no armazenamento local de análise. O GitHub recebe somente este relatório derivado, sem fontes de teste, binários, logs crus, endereços de processo não necessários ou dumps do kernel.

## Referências internas

[1]: ../reports/PRIORIDADES_CS_IB_GPU_EXECUTION_Xclipse_940.md "Prioridades de command submission, IB, fences e execução GPU"
[2]: ../docs/validation-and-evidence.md "Protocolo de validação e evidências"
[3]: ../source-analysis/xclipse-2026-09-06-evidence-map.md "Mapa público de evidências da coleta"

[1] [2] [3]

> Este relatório descreve somente os resultados técnicos permitidos pela análise do pacote recebido. Aceitação de ioctl não é sinônimo de execução GPU.
