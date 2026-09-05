# Project Status

**Status date:** 2026-09-05  
**Initial target:** Samsung Xclipse 940 / SM-S721B / XO940  
**Repository posture:** private, evidence collection and documentation phase

## Executive status

The project is at **Phase 0: governance and inventory**, with the Quick Share package now hashed, integrity-checked, extracted outside Git, and indexed. The archive SHA-256 is `cd73e2fa24ac083b9babf243b39772065b060abc9cd30fb476acabe74433e67f`; it contains 17 members, including a 405 MB Samsung source release, probe binaries/source, reports, PDFs, and logs. The archive remains external evidence because license and redistribution status are not yet complete.

The project is not yet at the driver bring-up phase. No statement in this file should be read as proof of a working independent ICD, custom queue submission, compute execution, ISA decoding, compiler lowering, or Mesa integration.

## Evidence ledger

| Area | Current statement | Evidence class | What is still required |
| --- | --- | --- | --- |
| Device | SM-S721B (`r12s`), platform `erd9945`, hardware `s5e9945`. | Confirmed from device logs | Correlate exact build with source revision. |
| Target GPU | Xclipse 940 / XO940; family `147 (MGFX)`, device `0x73a0`. | Confirmed for capture | Keep later revisions separate. |
| SGPU DRM | `/dev/dri/renderD128` bound to `sgpu`; display is separate on `renderD129`. | Confirmed | Map complete runtime ABI. |
| ASIC/queues | GFX 1 × 10.0 rings `0xf`; COMPUTE 1 × 10.0 rings `0x7`; DMA 0. | Confirmed for capture | Safe ring and queue lifecycle remain open. |
| Memory/VM | 64 KiB GTT BO, CPU touch, VA map/unmap and close worked. | Confirmed for capture | GPU access, residency, cache, and page tables remain open. |
| Firmware | SGPU `2.23.0`, RTL `0x4ea15`; ME/MEC/PFP/RLC metadata observed. | Confirmed for capture | Map exact blobs/load order and licenses. |
| Vendor ICD path | `/vendor/lib64/hw/vulkan.samsung.so` (44,423,944 bytes). | Confirmed by supplied report | Identify production loading bridge. |
| Android loader path | `/system/lib64/libvulkan.so` (240,208 bytes). | Confirmed by supplied report | Compare process namespaces. |
| Termux Vulkan probe | Only `llvmpipe`, vendor `0x10005`, visible. | Confirmed for this process | Test from a production graphics namespace. |
| Samsung Vulkan enumeration | Vendor `0x144d` not visible in observed namespace. | Confirmed for this environment | Does not prove ICD absence. |
| Direct ICD loading | Linker namespace blocked `/vendor/lib64/hw`; attempt ended in SIGSEGV. | Confirmed for this approach | Do not repeat without a different bridge. |
| SGPU probe class | Memory/VM bring-up probe; explicitly submits nothing. | Confirmed from source | Not a compute or rendering test. |
| Vulkan probe class | Pipeline metadata probe; no queue, dispatch, submit, or readback. | Confirmed from source | Not a compute execution test. |
| Root/SELinux | Root plus temporary `Permissive` did not change loader result; restored to `Enforcing`. | Confirmed for experiment | Root is not namespace membership. |
| Kernel/UAPI | `sgpu_drm.h`, SGPU driver, s5e9945 DT, IOMMU/DMA-BUF paths inventoried. | Confirmed by source | File-level licensing and runtime correlation pending. |
| Custom GPU work | No custom submission or independent driver demonstrated. | Confirmed negative status | Do not advance before queue/recovery evidence. |
| ISA/compiler | No decoded instruction format or compiler backend demonstrated. | Confirmed negative status | Controlled shader correlation remains future work. |

## Entry criteria for the next phase

Phase 0 is complete only when the source archive has a cryptographic hash, a complete file inventory, a reviewed license inventory, a device/build identity, a firmware inventory, and a list of open questions. The first five inventory items now exist; license review and source-path correlation remain open. The next phase is platform/kernel mapping, not layer development.

## Open questions

The following questions are intentionally unresolved:

1. Which exact Xclipse revision and firmware build correspond to the SM-S721B sample?
2. Which DRM device node, UAPI, memory manager, IOMMU configuration, and queue interfaces are exposed?
3. Which parts of the Samsung source are redistributable, and which are reference-only?
4. Which Android process and linker namespace load the vendor ICD in production?
5. Which firmware components participate in boot, scheduling, reset, and command processing?
6. Can a safe, reversible compute path be observed without reusing opaque command buffers?
7. Which shader binary fields are stable across controlled inputs and revisions?

## Change discipline

Any new claim must link to a report, raw artifact, source path, or reproduction command. A test harness name alone is never evidence that the underlying GPU operation ran.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
