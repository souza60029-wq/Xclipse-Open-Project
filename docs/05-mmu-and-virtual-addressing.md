# MMU and Virtual Addressing

**Status:** basic VA mapping confirmed; page-table format and fault recovery remain open.

The observed SGPU device reports a virtual-address offset of `0x8000000`, maximum address `0x800000000000`, alignment `0x1000`, and GART page size `0x1000`. The raw probe uses the same offset for a temporary 64 KiB mapping with readable and writable flags, then removes it.

| Field | Observed value | Confidence |
| --- | --- | --- |
| VA offset | `0x8000000` | Confirmed for capture |
| VA maximum | `0x800000000000` | Confirmed for capture |
| VA alignment | `0x1000` | Confirmed for capture |
| GART page size | `0x1000` | Confirmed for capture |
| Probe flags | readable + writable | Confirmed from source |
| Page faults at query | `0` | Confirmed at probe time |

The UAPI header exposes `AMDGPU_VA_OP_MAP`, `AMDGPU_VA_OP_UNMAP`, `AMDGPU_VM_PAGE_READABLE`, `AMDGPU_VM_PAGE_WRITEABLE`, and `AMDGPU_VM_PAGE_EXECUTABLE`. It also defines memory-type flags and delayed page-table update semantics. These definitions are an interface surface, not a complete hardware page-table specification.

## What remains unknown

The project still needs the page-table hierarchy, PTE encoding, invalidation timing, fault reporting, residency rules, protected-memory behavior, and interaction between TTM/GART/IOMMU and the SGPU firmware. The probe's successful map/unmap sequence is a prerequisite result, not proof that executable GPU virtual memory is ready for custom work.

## Safety boundary

Do not test arbitrary GPU virtual addresses, executable mappings, or opaque command buffers until reset and recovery are understood. Keep mappings bounded and cleanly released.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://docs.kernel.org/gpu/drm-mm.html "Linux DRM memory management documentation"
