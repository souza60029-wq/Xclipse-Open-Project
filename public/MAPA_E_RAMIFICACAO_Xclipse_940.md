# Mapa e ramificação técnica da Samsung Xclipse 940

**Projeto:** XO940
**Hardware de referência:** Samsung Xclipse 940 — SM-S721B / s5e9945
**Finalidade:** documentar a infraestrutura observada, os caminhos de reprodução e as áreas que exigem investigação adicional, incluindo GPU, NPU e seus limites de integração.

## Legenda cromática

| Cor | Camada | Uso no mapa |
| --- | --- | --- |
| Azul | Plataforma e Device Tree | `sgpu@22200000`, G3DCORE, clocks, reset, IRQ, DVFS e thermal. |
| Roxo | Kernel, DRM e UAPI | render nodes, GEM/BO, VM, contextos, CS, IB e interfaces de baixo nível. |
| Verde | Memória e endereçamento | VA, PTE/PDE, IOMMU, DMA-BUF, heaps e sincronização de mapeamento. |
| Laranja | Execução GPU | scheduler, rings, command processor, fences, retire e readback. |
| Cinza-azulado | Android e integração | loader, namespaces, SurfaceFlinger, RenderThread, Gralloc e SELinux. |
| Azul-petróleo | NPU e aceleração neural | NNAPI, ENN, grafos, compilação, execução, checksums e comparação com CPU. |
| Cinza-escuro | Evidência e organização técnica | probes, hashes, mapas, matrizes de variantes e classificação. |
| Vermelho | Caminhos de investigação e possíveis falhas | ICD layers, root, desbloqueio, permissões, componentes customizados, ISA, packets e integração experimental. Vermelho indica risco ou investigação aberta; não indica vulnerabilidade confirmada. |

![Mapa técnico da infraestrutura Xclipse 940](MAPA_Xclipse_940.png)

## Ramificação técnica

```text
SoC Exynos / Android
├── Xclipse 940 / bloco G3D
│   ├── Device Tree e plataforma
│   │   ├── sgpu@22200000
│   │   ├── pd_g3dcore
│   │   ├── MMIO: gpu, doorbell, debug, pwrctl, sysreg, htu
│   │   ├── IRQ: SGPU e GPU-AFM
│   │   ├── clocks, DVFS, IFPO, thermal e reset
│   │   └── memória reservada, DMA heap, BTS e SysMMU/IOMMU
│   ├── Kernel SGPU / DRM
│   │   ├── sgpu_drm.h / UAPI
│   │   ├── GEM, BO, TTM e DMA-BUF
│   │   ├── VM, PTE, PDE, VA e flush
│   │   ├── context, scheduler, prioridade e seqno
│   │   ├── CS, chunks, IB e BO_LIST
│   │   ├── GFX/COMPUTE, rings e command processor
│   │   ├── fences, syncobjs, dependências e retire
│   │   └── firmware, timeout, reset e recovery
│   └── Android gráfico
│       ├── libdrm_sgpu.so
│       ├── vulkan.samsung.so
│       ├── libOpenCL.so / libSGPUOpenCL.so
│       ├── SurfaceFlinger / RenderThread / Gralloc
│       └── linker namespaces, SELinux e HAL
├── NPU Exynos / aceleração neural
│   ├── interface NNAPI e dispositivo `enn`
│   ├── grafos FULLY_CONNECTED, INT8, atenção e softmax
│   ├── compilação de modelos e seleção de operações
│   ├── execução, checksum e comparação com CPU
│   ├── limites de multi-saída, BATCH_MATMUL e ordem de operandos
│   ├── serviço público `IDevice/enn` acessível sem root
│   └── energia, frequência e teste sustentado
└── Reprodução e investigação
    ├── probes DRM e inventários runtime
    ├── coleta de ambiente e hashes
    ├── comparação entre variantes Device Tree
    ├── ICD layers e pontes de loader [INVESTIGAÇÃO]
    ├── root, desbloqueio e permissões [INVESTIGAÇÃO]
    ├── componentes customizados de userspace [INVESTIGAÇÃO]
    └── ISA, packets e compiler [INVESTIGAÇÃO]
```

## Caminho operacional da GPU

```text
Device Tree / power / clocks / reset
        ↓
SGPU kernel driver / DRM UAPI
        ↓
GEM/BO + VM/PTE/PDE + IOMMU
        ↓
context + BO_LIST + CS + IB
        ↓
scheduler + ring + command processor
        ↓
GPU GFX/COMPUTE
        ↓
fence / syncobj / retire
        ↓
readback ou apresentação
```

A cadeia descreve dependências técnicas. Cada etapa permanece classificada individualmente; a presença de uma etapa anterior não prova a conclusão das etapas posteriores.

## Caminho operacional da NPU

```text
Aplicação ou teste NNAPI
        ↓
NNAPI reference / dispositivo ENN
        ↓
modelFinish + createForDevices + compilationFinish
        ↓
grafo aceito e executado
        ↓
checksum / saída numérica / tempo
        ↓
comparação com CPU e análise térmica
```

A NPU possui API, runtime, compilador, limitações de grafo e critérios de readback próprios. A presença de NNAPI ou ENN não demonstra relação de execução com o render node da GPU.

### Cadeia rootless comprovada

```text
UID comum de aplicativo
        ↓
android.hardware.neuralnetworks.IDevice/enn
        ↓
modelFinish + compilação ENN
        ↓
Execution_compute sem root
        ↓
SOFTMAX correto: diferença máxima 0,000000
```

Essa cadeia foi executada por um processo sem root. Ela comprova a rota pública NNAPI/ENN para o grafo reproduzido. Não comprova a utilização direta da interface vendor `IEnnInterfaceAidl` nem do endpoint `/dev/vertex10`.

## NPU/NNAPI: evidências sanitizadas

| Operação | Resultado observado | Classificação |
| --- | --- | --- |
| `FULLY_CONNECTED` 1024×1024, INT8, quatro camadas | ENN: 1,1148 ms por execução e checksum 114987; referência CPU: 6,2407 ms; razão aproximada 5,60×. | Execução NNAPI observada com saída correlacionada. |
| `FULLY_CONNECTED` 1024×1024, ponto flutuante | ENN: 1,2092 ms; referência CPU: 0,1332 ms. | Execução observada; a referência CPU foi mais rápida. |
| Soma elementar | Resultado `[11, 22, 33, 44]` conforme esperado; 0,9218 ms por execução em 200 execuções. | Execução funcional mínima observada. |
| Softmax de 1024 elementos | Soma da saída 1,0000; ENN: 1,0264 ms; referência CPU: 0,0104 ms. | Resultado numérico observado; desempenho inferior à referência. |
| Softmax pequeno sem root | `IDevice/enn` encontrado; compilação e `Execution_compute` concluídos; saída `[0,032059, 0,087144, 0,236883, 0,643914]`; diferença máxima 0,000000. | Execução rootless confirmada para o grafo reproduzido. |
| Cadeia quantizada de quatro camadas | ENN: 1,1148 ms por execução; referência CPU: 6,2407 ms; checksum igual. | Execução e readback numérico correlacionados. |
| Atenção e `BATCH_MATMUL` | Algumas formas não foram aceitas na compilação ou não foram reportadas como suportadas. | Limitação específica do grafo, não falha geral da NPU. |
| Ordem dos operandos | Entradas declaradas antes de operandos compartilhados compilaram; a ordem inversa falhou em casos equivalentes. | Dependência estrutural observada no compilador/runtime NNAPI. |
| Pipeline de decodificação | Grafos separados Q/K/V e grafos híbridos compilaram em uma configuração; uma tentativa multi-saída falhou. | Resultado parcial e sensível à forma do grafo. |

O teste sustentado de 90 segundos registrou variação de tempo por janela e frequências de CPU durante as fases CPU e NPU. Como não houve acesso ao caminho térmico completo nessa coleta, os números não provam throttling da NPU; servem como baseline para novas coletas.

## Caminhos de investigação em vermelho

Os caminhos vermelhos são pontos que podem determinar a viabilidade de integração de componentes de userspace ou de carregamento alternativo. Eles incluem a relação entre ICD layers e o loader Android, as fronteiras de root e desbloqueio, permissões de `/dev/dri`, namespaces, SELinux, formato de packets, ISA, compiler e sincronização.

O mapa não afirma que qualquer caminho vermelho esteja desbloqueado. A rota NNAPI/ENN acima deixou de ser hipótese e passou a ser um caminho rootless confirmado para o grafo reproduzido. A interface vendor direta e o endpoint `/dev/vertex10` continuam sem rota rootless demonstrada. O objetivo dos caminhos restantes é localizar o contrato ou a falha sem confundir presença de componentes com acesso funcional.

## Proveniência e reprodução

A coleta deve registrar aparelho, build, kernel, firmware, permissões, contexto SELinux, comando, ambiente, saída bruta, resultado e SHA-256. Logs de telefonia, bateria, memória pessoal, caminhos privados e identificadores do usuário devem ser removidos dos derivados públicos. Os arquivos brutos permanecem fora da release.

## Referências internas

- `docs/validation-and-evidence.md`
- `source-analysis/device-tree-technical-map.md`
- `source-analysis/xclipse-2026-09-06-evidence-map.md`
- `STATUS.md`
