# NPU/ENN rootless — atualização de 18/09/2026

## Resultado principal

A nova coleta comprovou que um processo Android sem root consegue obter o serviço público `android.hardware.neuralnetworks.IDevice/enn`, compilar um modelo NNAPI e executar um grafo `SOFTMAX` no ENN com saída correta.

A saída produzida foi `[0.032059, 0.087144, 0.236883, 0.643914]`, igual ao valor esperado, com diferença máxima `0.000000`. A cadeia observada foi: descoberta do dispositivo `enn`, finalização do modelo, compilação, execução e leitura do resultado.

## O que é novidade

A descoberta transforma a rota NNAPI/ENN em um caminho comprovadamente **rootless** para os grafos reproduzidos. Ela não depende de copiar bibliotecas vendor para o aplicativo e não usa acesso direto ao device node da NPU.

A nova evidência também corrige a classificação anterior de `SOFTMAX`. O benchmark antigo mostrou desempenho ruim, mas o teste novo comprovou suporte e execução correta em um grafo pequeno. `SOFTMAX` deve ser classificado como funcional, porém ainda não otimizado para workloads maiores.

## Limites preservados

`BATCH_MATMUL` continua não suportado pelo dispositivo `enn` no formato testado. A interface proprietária `vendor.samsung_slsi.hardware.enn_aidl.IEnnInterfaceAidl/default` não foi descoberta pelo teste, nem com UID comum nem com root. O endpoint `/dev/vertex10` exige permissões elevadas e não teve ioctl funcional demonstrado.

A conclusão correta é que existe uma rota pública NNAPI/ENN rootless, não que todo o decoder transformer possa ser transferido à NPU. A execução completa ainda depende de particionamento, medição e fallback.

## Próximo caminho documentado

O XOP passa a registrar a seguinte estrada:

```text
Aplicativo sem root
        ↓
Android NNAPI: IDevice/enn
        ↓
HAL ENN Samsung
        ↓
Subgrafo aceito pelo driver
        ↓
Execução NPU e readback
```

A futura camada de pesquisa pode ser construída sobre essa interface pública, mantendo `FULLY_CONNECTED` INT8 e outros subgrafos aceitos no ENN, enquanto operações não suportadas permanecem na CPU ou em outro backend.

## Proveniência

Fonte: pacote `Exynos_NPU.zip` obtido em 18/09/2026. SHA-256: `873a0f8d3ff4d8e9a4168aafab0349f5becfa539cfc9b03d8a7033e3acb08af9`.
