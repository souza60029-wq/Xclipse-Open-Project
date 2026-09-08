# Validation Results
# Validation Results

This document is a results index, not a claim that all planned milestones have passed. It records the strongest evidence currently extracted from the supplied package.

| Milestone | Current state | Evidence status |
| --- | --- | --- |
| Platform identity | SM-S721B (`r12s`), `erd9945`, `s5e9945`; SGPU node at `/dev/dri/renderD128`. | Confirmed from device logs. |
| Kernel/UAPI | `sgpu_drm.h`, SGPU driver tree, s5e9945 Device Tree, IOMMU and DMA-BUF paths inventoried. | Confirmed by source; runtime correlation pending. |
| Android loader | System loader and vendor ICD paths are present; Termux sees only llvmpipe. | Confirmed for observed process. |
| GPU enumeration | DRM probe identifies MGFX family `147`, device `0x73a0`; Vulkan vendor `0x144d` is not visible in Termux namespace. | Confirmed for separate paths. |
| Memory/VM | 64 KiB GTT BO, CPU mmap/touch, VA map/unmap and close succeeded. | Confirmed for capture. |
| Queue inventory | One GFX and one COMPUTE IP instance reported; ring masks `0xf` and `0x7`. | Confirmed for capture; no submit. |
| Firmware metadata | SGPU `2.23.0`, RTL `0x4ea15`, component versions recorded. | Confirmed for capture. |
| Compute execution | No queue submit, dispatch, synchronization, or validated readback exists in supplied probes. | Not demonstrated. |
| ISA | No instruction format decoded with confidence; pipeline IR path not reached on Samsung GPU. | Not demonstrated. |
| Compiler | No Xclipse compiler backend. | Not started. |
| Vulkan | Instance and pipeline metadata probe exists; no independent target-device execution path. | Partial observation only. |
| Layers | No diagnostic layer proven on Samsung ICD. | Not started. |
| Driver | No independent Vulkan driver feature demonstrated. | Not started. |

## Interpretation rule

A row can move to “confirmed” only when the repository contains an evidence report that proves the exact milestone. The existence of planned directories, initializers, capability queries, pipeline creation, or buildable stubs does not change this table.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. O relatório sanitizado está em [`reports/xclipselogs-2026-09-07-analysis.md`](reports/xclipselogs-2026-09-07-analysis.md). Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
