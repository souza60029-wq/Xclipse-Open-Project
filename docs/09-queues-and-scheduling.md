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
