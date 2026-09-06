# Rotas de investigação e falhas observadas

## Rota prioritária

A rota prioritária começa pelo Device Tree em runtime e segue até o primeiro job GFX controlado. O trabalho deve reproduzir power-domain, MMIO, IRQ, memória reservada, DMA heap, IOMMU, BO, VM, scheduler e fence nessa ordem. Essa sequência reduz o risco de atribuir uma falha de plataforma a comandos ou ISA.

## Bloqueios atuais

| Bloqueio | Evidência | Consequência |
| --- | --- | --- |
| workload não isolado | RenderThread, Gralloc, Chrome e compositor aparecem nas janelas | não atribuir qualquer job ao cliente estudado |
| fence clássica indisponível em parte da coleta | eventos `amdgpu_fence*` aparecem como unavailable em P4.12 | correlacionar por seqno, tracepoints disponíveis e saída controlada |
| compute não demonstrado | P5.04 não contém CS/scheduler/IB compute | não declarar dispatch ou readback |
| ABI vendor fechada | bibliotecas e exports estão presentes, mas dependências e namespaces são específicos | não assumir reutilização fora do Android vendor |
| formatos internos incompletos | PTE, IB e símbolos são observados sem especificação pública completa | manter implementação em modo experimental e reversível |

## Rotas de estudo

1. **Cliente GFX mínimo:** criar um marcador de processo, identificar contexto e capturar apenas o intervalo do cliente; validar uma saída em buffer.
2. **Memória mínima:** reproduzir BO, map, VM update, flush e unmap sem submeter comandos complexos.
3. **Conclusão:** usar `sched_job`, `context`, `seqno`, `dma_fence` disponível e leitura de estado de ring para provar término.
4. **Compute:** começar por identificação OpenCL e um buffer pequeno; separar criação de contexto, compilação, dispatch e readback.
5. **Vulkan:** somente depois do contrato de memória e submissão; usar a pilha vendor como referência observacional, não como ABI aberta.

## O que deve ser evitado

Não publicar scripts de captura nem orientar a execução de probes como se fossem testes de funcionalidade. Não escrever dados arbitrários em MMIO ou firmware. Não substituir a validação de um job controlado por strings de uma biblioteca ou por exports ELF.
