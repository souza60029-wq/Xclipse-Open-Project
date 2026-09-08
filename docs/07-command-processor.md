# Command Processor

**Status:** UAPI and source paths identified; no custom command submission has been demonstrated.

## Purpose

The supplied `sgpu_raw_probe.c` intentionally stops before command submission. Its source comment states `Submits NOTHING to the GPU`; the observed output ends after GEM close with `DONE fails=2`. Therefore the package does not contain a packet capture or a proven command-processor execution trace.

The Samsung UAPI nevertheless exposes an AMDGPU-derived command-stream interface. `drm_amdgpu_cs_in` carries a context ID, BO-list handle, chunk count, flags, and a pointer to chunks. `drm_amdgpu_cs_chunk` identifies an IB, fence, dependency, sync object, BO-handle, or timeline operation. `drm_amdgpu_cs_chunk_ib` carries a virtual address, byte size, IP type, IP instance, ring, and flags such as secure, preempt, cache synchronization, performance counter, and SQ thread trace.

| Evidence | What it establishes | What it does not establish |
| --- | --- | --- |
| UAPI `DRM_IOCTL_AMDGPU_CS` definition | A command submission contract exists in the source interface. | That the SGPU accepts every upstream AMDGPU packet or flag. |
| UAPI IB/chunk structures | The shape of a possible submission path. | The packet encoding placed inside an IB. |
| Observed GFX/COMPUTE ring masks `0xf` and `0x7` | Ring availability was reported by `HW_IP_INFO`. | Which ring is safe or correct for custom work. |
| `sgpu_raw_probe` | Memory and VA prerequisites worked. | Any GPU command executed. |
| `libdrm_sgpu.so` symbols | Wrappers for `amdgpu_cs_submit` and `sgpu_cs_submit` exist in the vendor library. | ABI compatibility or safe use outside its intended runtime. |

## Required next evidence

Before constructing an IB, document context creation, BO lists, firmware mediation, synchronization, ring selection, reset behavior, and a bounded recovery path. A real execution test must submit known work and validate an independent result. Opaque command-buffer replay is out of scope until provenance and safety are understood.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. O relatório sanitizado está em [`reports/xclipselogs-2026-09-07-analysis.md`](reports/xclipselogs-2026-09-07-analysis.md). Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
