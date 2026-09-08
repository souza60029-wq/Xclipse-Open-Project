# Project Status

**Status date:** 2026-09-06
**Internal project name:** XO940
**Initial target:** Samsung Xclipse 940 / SM-S721B
**Repository posture:** private, evidence collection and documentation phase

## Executive status

The project is at the **Phase 0 → Phase 1 boundary: Device Tree and platform mapping**. The original Quick Share package and the new results package are hashed, integrity-checked, extracted outside Git, and indexed. The new results package SHA-256 is `59ac6221373572024c0967d642f9ead1f970c6791ff2428cb5c49ae73a5fe7fc`; it contains stage reports, Etapa 4 logs, Etapa 5 compiler analysis, and the vendor ICD. The archives and vendor binaries remain external evidence because license and redistribution status are not yet complete.

The project is not yet at the driver bring-up phase. No statement in this file should be read as proof of a working independent ICD, custom queue submission, compute execution, ISA decoding, compiler lowering, or Mesa integration.

The 2026-09-06 collection strengthened platform, VM and vendor GFX scheduling. The 2026-09-07 xclipselogs additionally show a client-owned `DRM_IOCTL_AMDGPU_CS` accepted by the KMD for one specific IB/chunk input; execution, fence and readback remain unproven. The public interpretation is documented in `reports/estudo-detalhado-xclipse-940-2026-09-06.md`.

## Evidence ledger

| Area | Current statement | Evidence class | What is still required |
| --- | --- | --- | --- |
| Device | SM-S721B (`r12s`), platform `erd9945`, hardware `s5e9945`. | Confirmed from device logs | Correlate exact build with source revision. |
| Target GPU | Xclipse 940; family `147 (MGFX)`, device `0x73a0`. | Confirmed for capture | Keep later revisions separate. |
| Device Tree | `/sgpu@22200000`, compatible `samsung-sgpu,samsung-sgpu`, common/EVT0 DTSI paths, six register regions, interrupts, power and DMA properties. | Source-backed and partially runtime-correlated | Close clocks, reset, IOMMU, revision and power sequencing. |
| SGPU DRM | `/dev/dri/renderD128` bound to `sgpu`; display is separate on `renderD129`. | Confirmed | Map complete runtime ABI. |
| ASIC/queues | GFX 1 × 10.0 rings `0xf`; COMPUTE 1 × 10.0 rings `0x7`; DMA 0. | Confirmed for capture | Safe ring and queue lifecycle remain open. |
| Memory/VM | 64 KiB GTT BO, CPU touch, VA map/unmap and close worked. | Confirmed for capture | GPU access, residency, cache, and page tables remain open. |
| Firmware | SGPU `2.23.0`, RTL `0x4ea15`; ME/MEC/PFP/RLC metadata observed. | Confirmed for capture | Map exact blobs/load order and licenses. |
| Vendor ICD path | `/vendor/lib64/hw/vulkan.samsung.so` (44,423,944 bytes), mapped in valid SurfaceFlinger snapshot. | Confirmed in production process | Capture detailed Vulkan enumeration from that process. |
| Android loader path | `/system/lib64/libvulkan.so` mapped in SurfaceFlinger; Termux is a different mount namespace. | Confirmed | Document the supported bridge and ABI. |
| Termux Vulkan probe | Only `llvmpipe`, vendor `0x10005`, visible. | Confirmed for this process | Do not generalize to production SurfaceFlinger. |
| Samsung Vulkan enumeration | Production ICD mapping is confirmed; full `vkEnumeratePhysicalDevices` output is not yet captured. | Partially confirmed | Capture vendor/device IDs and extensions in supported process. |
| Direct ICD loading | Linker namespace blocked `/vendor/lib64/hw`; attempt ended in SIGSEGV. | Confirmed for this approach | Do not repeat without a different bridge. |
| SGPU probe class | Memory/VM bring-up probe; explicitly submits nothing. | Confirmed from source | Not a compute or rendering test. |
| Photo Remaster compute path | Dedicated service loads Vulkan Samsung, libdrm SGPU and SGPU OpenCL and holds renderD128. | Confirmed path/correlation | Capture controlled kernel, synchronization and readback. |
| Vulkan probe class | Pipeline metadata probe; no queue, dispatch, submit, or readback. | Confirmed from source | Not a compute execution test. |
| Root/SELinux | Root plus temporary `Permissive` did not change loader result; restored to `Enforcing`. | Confirmed for experiment | Root is not namespace membership. |
| Kernel/UAPI | `sgpu_drm.h`, SGPU driver, s5e9945 DT, IOMMU/DMA-BUF paths inventoried. | Confirmed by source | File-level licensing and runtime correlation pending. |
| Custom GPU work | One client-owned CS ioctl was accepted; independent GPU execution remains unproven. | Partial positive / no execution proof | Correlate fence, kernel log, GPU effect and readback. |
| ISA/compiler | Static vendor compiler/encoding infrastructure strongly evidenced; no native ISA stream captured. | Confirmed static evidence | Correlate controlled shader, binary and runtime instruction. |

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

## Current five priorities

The old five-item ordering was replaced. The current priorities are: (1) complete Device Tree/platform mapping; (2) memory, IOMMU, DMA-BUF and buffer protection; (3) supported Android vendor path; (4) firmware, queues and recovery; and (5) controlled compute/shader capture. Five lower-cost read-only discoveries are listed in the same report to accelerate progress without skipping safety prerequisites.

The technical branch means the hardware map of Device Tree, kernel, GPU, memory, power, IOMMU and Android paths. It is not the repository directory tree. Tests, capture scripts, raw traces, dumps, vendor libraries and binaries remain excluded from public publication.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
