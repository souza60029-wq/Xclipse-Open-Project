# 5 dois meios — primeiros cinco itens essenciais

Este é o primeiro recorte operacional do projeto XO940. Os cinco itens abaixo são os bloqueadores técnicos prioritários. Eles não são cinco testes já concluídos. São cinco descobertas que precisam ser obtidas com evidência suficiente antes de promover o projeto para submissão de comandos, compute, ISA, compiler ou driver independente.

## 1. Loader Android e enumeração do `VkPhysicalDevice` Samsung

**Pergunta:** qual processo Android suportado carrega `/vendor/lib64/hw/vulkan.samsung.so`, em qual linker namespace, por qual manifest/HAL e com qual ABI?

**Estado atual:** a biblioteca vendor foi localizada, mas o processo Termux enumerou somente `llvmpipe`, vendor `0x10005`. O vendor Samsung `0x144d` não apareceu naquele namespace. O `dlopen` direto foi bloqueado pelo linker namespace e terminou em `SIGSEGV`; isso descarta aquela abordagem específica, não o ICD em geral.

**Teste necessário:** mapear o processo gráfico suportado e capturar namespace, loader, manifest/HAL, vendor ID, device ID, build, comando e saída bruta.

**Sucesso mínimo:** um processo suportado enumera o dispositivo Samsung `0x144d`, com o caminho de carregamento reproduzível.

## 2. Contrato DRM, BO, VA, IOMMU e cache

**Pergunta:** qual é o contrato observável entre usuário, SGPU DRM, GEM/BO, DMA-BUF, IOMMU, VA e coerência de cache?

**Estado atual:** `/dev/dri/renderD128` está ligado ao SGPU. O probe criou um BO GTT de 64 KiB, realizou mapeamento pela CPU, tocou a memória, mapeou e desmapeou VA e fechou o objeto. Isto prova uma trilha de bring-up de memória e VM; não prova que a GPU acessou o BO.

**Teste necessário:** repetir as operações de forma reversível e registrar alinhamento, flags, VA, permissões, DMA-BUF, cache, falhas e relação com page faults.

**Sucesso mínimo:** documentação reproduzível do caminho de memória e das limitações, sem promover mapeamento de CPU a execução de GPU.

## 3. Contexto, ring, IB, fence e recuperação

**Pergunta:** como são criados contexto, BO list, chunk, IB, ring, fence/syncobj e reset seguro?

**Estado atual:** o UAPI `sgpu_drm.h` define estruturas AMDGPU-derived para contexto, BO list, command submission, chunks, IBs, fences e sincronização. O hardware reporta GFX com máscara de rings `0xf` e COMPUTE com máscara `0x7`. Nenhum submit real foi demonstrado.

**Teste necessário:** correlacionar código SGPU, firmware, scheduler, reset, timeouts e logs. Só depois preparar um experimento bounded, com timeout e recuperação definida.

**Sucesso mínimo:** um procedimento que possa ser interrompido, sincronizado e recuperado sem deixar o aparelho em estado incerto.

## 4. Primeiro dispatch compute com readback

**Pergunta:** a GPU XO940 consegue executar uma operação mínima, sincronizar sua conclusão e produzir um valor verificável?

**Estado atual:** não demonstrado. O probe SGPU declara que não submete nada. O probe Vulkan cria um pipeline candidato, mas não obtém queue, não grava `vkCmdDispatch`, não chama `vkQueueSubmit`, não espera fence e não faz readback.

**Teste necessário:** submeter uma operação determinística em um ambiente Samsung real, usar um recurso conhecido, aguardar a sincronização e comparar o valor produzido com o esperado.

**Sucesso mínimo:** evidência bruta do BO antes e depois, submit, completion, readback e recuperação. A mensagem `compute pipeline ok` isolada não é suficiente.

## 5. Correlação shader–binário–ISA

**Pergunta:** quais campos do shader compilado correspondem a instruções, operandos, registradores, waves, controle de fluxo e recursos da ISA Xclipse?

**Estado atual:** não demonstrado. O probe Vulkan contém entrada SPIR-V e tenta consultar propriedades de pipeline, mas o caminho observado foi `llvmpipe`; não há binário Samsung de shader nem formato de instrução Xclipse decodificado com confiança.

**Teste necessário:** primeiro obter a saída pelo ICD Samsung real. Depois comparar pares de shaders controlados, hashes, tamanhos, metadados e diferenças binárias.

**Sucesso mínimo:** ao menos uma forma de instrução ou campo decodificada com confiança e reproduzida em mais de um caso controlado.

## Ordem de trabalho imediata

A ordem correta é **1 → 2 → 3 → 4 → 5**. Não se deve começar pelo item 4 enquanto o item 1 não explicar como alcançar o ICD Samsung, nem enquanto os itens 2 e 3 não definirem memória, submissão, sincronização e recuperação. O item 5 depende de um caminho real de compilação ou captura Samsung; pipeline metadata de `llvmpipe` não serve como evidência da ISA XO940.

## Regra de promoção

Um item só passa de hipótese para confirmado quando o repositório contém comando reproduzível, saída bruta, contexto do dispositivo, relatório de interpretação e nível de confiança. Nomes de diretório, inicializadores, capability queries, criação de pipeline e códigos de retorno não promovem um item a teste de execução.

## Referências

[1]: ../STATUS.md "Status do projeto XO940"
[2]: initial-evidence.md "Relatório de evidências iniciais"
[3]: ../docs/07-command-processor.md "Command processor"
[4]: ../docs/19-compute-pipeline.md "Compute pipeline"
[5]: ../docs/14-shader-isa.md "Shader ISA"
