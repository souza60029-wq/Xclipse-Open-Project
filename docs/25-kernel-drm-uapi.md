# Kernel DRM and UAPI

**Status:** SGPU node, UAPI header, source implementation paths, and a reversible memory probe are documented.

The observed DRM layout contains SGPU `card0`/`renderD128` and display `card1`/`renderD129`. `renderD128` is bound to `/sys/bus/platform/drivers/sgpu`; its uevent is `samsung-sgpu,samsung-sgpu` at `/sgpu@22200000`.

The source release contains `kernel/include/uapi/drm/sgpu_drm.h`. It defines AMDGPU-derived ioctl numbers for GEM creation/mmap, contexts, BO lists, command submission, info queries, GEM VA, waits, VM, fences, and scheduling. It adds Samsung-specific instance-data and memory-profile operations. It defines GFX/COMPUTE/DMA IP types, firmware and device-info queries, page-fault queries, IB/chunk submission structures, fences, dependencies, sync objects, and timeline operations.

The probe exercised `DRM_IOCTL_AMDGPU_INFO`, `DRM_IOCTL_AMDGPU_GEM_CREATE`, `DRM_IOCTL_AMDGPU_GEM_MMAP`, `DRM_IOCTL_AMDGPU_GEM_VA`, and `DRM_IOCTL_GEM_CLOSE`. The DRM version query failed with `errno=14`, and `SGPU_KMD_VERSION` failed with `errno=22`; device info, IP info, firmware, page faults, GEM creation, CPU mapping, VA mapping/unmapping, and close returned the results recorded in `reports/initial-evidence.md`.

| Layer | Confirmed | Not yet confirmed |
| --- | --- | --- |
| Node discovery | SGPU render node and platform binding. | Full ABI behavior across builds. |
| Device query | MGFX family, revision, IP counts, rings, VA limits. | Meaning of every field for an open driver. |
| Memory | 64 KiB GTT BO, CPU map/touch, VA map/unmap. | GPU access, residency, cache protocol. |
| Command submission | UAPI definitions and source/vendor symbols. | Safe custom context, IB, submit, fence, and readback. |

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://docs.kernel.org/gpu/drm-uapi.html "Linux DRM userspace API documentation"
