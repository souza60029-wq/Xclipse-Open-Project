# Memory Model

**Status:** first reversible memory path confirmed; full residency, cache, and synchronization model pending.

The SGPU probe successfully executed a bounded memory sequence on `/dev/dri/renderD128`: it requested a 64 KiB GEM object in the GTT domain with 4 KiB alignment, obtained a handle, queried a mmap offset, mapped it into the process, wrote `0xAB` across the allocation, unmapped the CPU view, mapped the object into GPU virtual address `0x8000000`, unmapped that VA range, and closed the GEM handle.

| Operation | Observed result | Interpretation |
| --- | --- | --- |
| `DRM_IOCTL_AMDGPU_GEM_CREATE` | `ok handle=1` | A GTT-domain GEM allocation path worked for this request. |
| `DRM_IOCTL_AMDGPU_GEM_MMAP` + `mmap` | `mmap+touch ok` | CPU mapping and access worked for this buffer. |
| VA map | `ok @0x8000000` | A VA mapping path accepted the requested range and flags. |
| VA unmap | `ok` | The mapping was removed in the same probe. |
| GEM close | `ok` | The probe cleaned up the handle. |
| GPU page faults | `faults=0` | No page faults were reported at the query point. |

The source UAPI defines AMDGPU-derived domains and flags, including GTT, VRAM, CPU access, uncached, encrypted, and explicit synchronization flags. It defines `drm_amdgpu_gem_va` with map, unmap, clear, and replace operations, plus readable, writable, executable, and memory-type flags.

## Important limits

The successful CPU map does not prove that a GPU instruction read or wrote the buffer. The VA map does not prove that a command processor consumed the mapping. No command stream was submitted by `sgpu_raw_probe.c`, and no GPU readback was performed. The next memory phase must therefore study cache visibility, DMA-BUF ownership, residency, page tables, faults, and synchronization before any custom submission.

The device capture reports `dma-coherent` in the Device Tree and `ids_flags=0x4` in the device information. These are source/driver signals that require careful interpretation; they are not a complete cache-coherency specification.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://docs.kernel.org/gpu/drm-mm.html "Linux DRM memory management documentation"
