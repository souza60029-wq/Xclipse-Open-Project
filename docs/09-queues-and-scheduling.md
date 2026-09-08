# Queues and Scheduling

**Status:** IP inventory confirmed; queue lifecycle and safe submission remain unvalidated.

The raw SGPU probe reports one GFX IP instance, version `10.0`, ring mask `0xf`, IB alignment `32/32`; one COMPUTE IP instance, version `10.0`, ring mask `0x7`, IB alignment `32/32`; and zero DMA instances. These values describe what the kernel query returned for this sample. They do not identify a safe ring for a new user-space submission.

The source release includes `amdgpu_ring`, `amdgpu_job`, `amdgpu_sched`, `amdgpu_fence`, `amdgpu_sync`, context, BO-list, and reset-query code in the SGPU module. The UAPI includes context priorities, reset status, dependencies, fences, sync objects, timeline wait/signal chunks, and scheduler priority operations.

| Question | Current answer | Evidence label |
| --- | --- | --- |
| How many GFX/COMPUTE instances were reported? | One each. | Confirmed for capture |
| Which ring masks were reported? | GFX `0xf`; COMPUTE `0x7`. | Confirmed for capture |
| Was work submitted by the supplied probe? | No. | Confirmed from source |
| Is preemption supported? | The UAPI exposes preempt-related flags; runtime behavior is not proven. | Probable/source-backed |
| Is reset status query available? | Context and reset-query structures exist in UAPI and vendor symbols. | Confirmed by source |
| Is a queue completion path proven? | No. | Confirmed negative status |

## Next experiment boundary

The next queue experiment must begin with context and synchronization documentation, use a fresh bounded BO, submit only a known minimal IB, wait with a documented fence or sync object, and have a recovery procedure. It must never infer execution from a successful ioctl alone.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://docs.kernel.org/gpu/drm-uapi.html "Linux DRM userspace API documentation"

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
