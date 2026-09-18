# NPU Exynos: NNAPI/ENN rootless

**Data da atualização:** 2026-09-18  
**Dispositivo de referência:** Samsung SM-S721B / Exynos 2400  
**Classificação:** execução NNAPI/ENN confirmada sem root para os grafos reproduzidos

## Conclusão

A rota pública NNAPI/ENN já foi demonstrada por um processo sem root. O processo obteve o serviço `android.hardware.neuralnetworks.IDevice/enn`, encontrou dois dispositivos NNAPI (`enn` e `nnapi-reference`), compilou e executou um grafo `SOFTMAX` no dispositivo `enn` e recebeu uma saída numérica correta.

A prova não demonstra que o serviço proprietário `vendor.samsung_slsi.hardware.enn_aidl.IEnnInterfaceAidl/default` esteja aberto a aplicativos. As tentativas de descobrir esse serviço retornaram nulo tanto com UID comum quanto com root. Portanto, o caminho comprovado para um aplicativo é **NNAPI pública → HAL ENN → NPU**, e não a interface vendor proprietária direta.

## Prova principal sem root

O teste `item10_nnapi_hal_20260917_235059` obteve um Binder remoto para `android.hardware.neuralnetworks.IDevice/enn`. O `AIBinder_ping()` retornou `STATUS_OK`. O teste `item11_softmax_enn_20260918_000604` encontrou o dispositivo `enn` e reportou `SOFTMAX` como suportado. O teste `item12_exec_enn_20260918_001024` completou `ModelFinish`, compilação e `Execution_compute` sem root.

A saída do driver foi `[0.032059, 0.087144, 0.236883, 0.643914]`, igual ao resultado esperado, com diferença máxima `0.000000`. Essa é uma prova de execução funcional no dispositivo ENN por um processo comum. Ela não é apenas enumeração, compilação ou presença de biblioteca.

## O que mudou em relação à análise anterior

A análise anterior tratava `SOFTMAX` como rejeitado ou inviável com base no benchmark antigo. A nova coleta separa duas perguntas diferentes:

1. **O ENN consegue executar um SOFTMAX pequeno?** Sim, comprovado sem root.
2. **O ENN é rápido para um SOFTMAX maior?** Ainda não. O benchmark anterior mediu aproximadamente 1,0264 ms no ENN contra 0,0104 ms na CPU de referência para um vetor de 1024 elementos.

Assim, `SOFTMAX` deve ser removido da lista de operações universalmente rejeitadas. A classificação correta é: **suportado em pelo menos um grafo pequeno, porém com desempenho ruim no benchmark disponível**.

`BATCH_MATMUL` continua sem suporte no dispositivo testado. No teste corrigido, a operação foi adicionada ao modelo, mas `getSupportedOperationsForDevices` reportou que o `enn` não a suportava e a compilação terminou com status 4.

## Superfície do sistema confirmada

A coleta identificou:

- serviço `android.hardware.neuralnetworks.IDevice/enn`;
- manifesto VINTF `android.hardware.neuralnetworks-service-enn.xml`;
- binário `android.hardware.neuralnetworks-service-enn`;
- runtime `libenn_wrapper.so`, `libenn_engine.so`, `libenn_model.so` e bibliotecas relacionadas;
- `libenn_public_api_cpp.so` e `libnpu_compiler.so` no firmware;
- modelos `.nnc` presentes em componentes do sistema;
- processo HAL no domínio SELinux `hal_neuralnetworks_service_enn_default`;
- dispositivo `/dev/vertex10`, associado ao driver `exynos-npu` e ao nó `/npu_exynos`.

A presença desses elementos é evidência da arquitetura instalada. Ela não autoriza redistribuir bibliotecas, modelos, firmware ou código Samsung.

## Rota direta vendor: resultado atual

A interface `vendor.samsung_slsi.hardware.enn_aidl.IEnnInterfaceAidl/default` não foi descoberta pelo processo de teste. O resultado foi nulo em comparação entre UID comum e root. Isso pode significar que o serviço não está registrado naquele momento, que a interface é ativada apenas por consumidores específicos ou que há uma fronteira de namespace/política diferente. A coleta não permite afirmar que a interface não exista no sistema; permite afirmar que ela não foi uma rota funcional no teste realizado.

O endpoint `/dev/vertex10` foi aberto com root depois de desativar temporariamente o enforcing do SELinux, mas o UID comum recebeu `Permission denied`. Os ioctls testados retornaram `EINVAL` ou `EFAULT`. Portanto, o endpoint não é atualmente uma API rootless comprovada e não deve ser usado diretamente por aplicativos comuns.

## Implicação para a pesquisa NPU

Uma futura biblioteca de pesquisa pode começar como uma camada de espaço de usuário baseada em NNAPI pública. A primeira implementação deve usar o dispositivo `enn`, consultar operações com `getSupportedOperationsForDevices`, compilar somente grafos aceitos e manter CPU como fallback.

O caminho de modelo deve ser particionado. `FULLY_CONNECTED` INT8 já possui evidência de execução e checksum. `SOFTMAX` possui evidência de execução correta em grafo pequeno, mas precisa de otimização e medição. `BATCH_MATMUL` não pode ser enviado ao ENN no formato atual. Qualquer integração com outro aplicativo deve medir o custo real antes de escolher o backend.

## Nível de evidência

| Afirmação | Classificação |
| --- | --- |
| Serviço público `IDevice/enn` existe | Confirmada |
| UID comum consegue obter Binder remoto do ENN | Confirmada |
| UID comum consegue compilar um grafo pequeno no ENN | Confirmada |
| UID comum consegue executar SOFTMAX no ENN | Confirmada |
| SOFTMAX é rápido em workloads maiores | Não demonstrada; benchmark anterior foi ruim |
| BATCH_MATMUL é aceito pelo ENN | Negativa para o formato testado |
| Interface vendor AIDL direta é utilizável por app | Não demonstrada |
| `/dev/vertex10` é utilizável sem root | Negativa no teste realizado |
| NPU inteira pode executar um decoder transformer | Não demonstrada |

## Proveniência

A atualização foi derivada do pacote `Exynos_NPU.zip` baixado em 2026-09-18. SHA-256 do ZIP: `873a0f8d3ff4d8e9a4168aafab0349f5becfa539cfc9b03d8a7033e3acb08af9`.

Os resultados públicos são derivados e sanitizados. Logs crus, comandos de coleta, binários compilados, bibliotecas vendor, firmware, modelos proprietários e dumps completos permanecem fora da distribuição pública.
