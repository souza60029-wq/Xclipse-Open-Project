# Initial Evidence Review

**Evidence set:** Quick Share package `Tudo sobre a xclipse.zip`  
**Archive SHA-256:** `cd73e2fa24ac083b9babf243b39772065b060abc9cd30fb476acabe74433e67f`  
**Review date:** 2026-09-05  
**Target:** Samsung SM-S721B / Xclipse 940 / XO940

## Executive conclusion

The supplied package contains a coherent bring-up record, a Samsung kernel/platform source release, raw device logs, a DRM probe, a Vulkan pipeline-properties probe, vendor binaries, and continuity reports. The strongest current result is **DRM/KMD and memory bring-up**, not a working open driver or a compute execution test.

The SGPU probe opens `/dev/dri/renderD128`, identifies the platform driver as `sgpu`, queries device information, hardware IP counts, firmware versions, and page faults, creates a 64 KiB GTT GEM object, maps and touches it from the CPU, maps and unmaps a GPU virtual address, and closes the object. Its source says `Submits NOTHING to the GPU`. The observed `DONE fails=2` is therefore a probe result with two failed queries, not a failed compute test.

The Vulkan probe opens the system loader, creates a Vulkan instance, enumerates physical devices, looks for vendor `0x144d`, creates a device and a trivial compute pipeline if the Samsung device is visible, and queries `VK_KHR_pipeline_executable_properties`. It does not call `vkGetDeviceQueue`, `vkQueueSubmit`, `vkCmdDispatch`, `vkCmdDraw`, or perform a readback. The line `compute pipeline ok` would mean pipeline creation succeeded, not that a compute dispatch ran.

In the supplied execution, Vulkan exposed only `llvmpipe` with vendor `0x10005`; Samsung vendor `0x144d` was not visible. Directly loading `/vendor/lib64/hw/vulkan.samsung.so` from the Termux namespace was blocked and ended in a segmentation fault. This makes the Android loader/namespace/ICD boundary the immediate blocker.

## Confirmed by device evidence

| Area | Observation | Evidence label |
| --- | --- | --- |
| Device identity | `MODEL=SM-S721B`, device `r12s`, platform `erd9945`, hardware `s5e9945`. | Confirmed |
| SGPU node | `/dev/dri/renderD128` exists and is bound to `/sys/bus/platform/drivers/sgpu`. | Confirmed |
| Display DRM | `/dev/dri/renderD129` is bound to `exynos-drm`; it is distinct from the SGPU render node. | Confirmed |
| Device Tree | `OF_COMPATIBLE_0=samsung-sgpu,samsung-sgpu`, full name `/sgpu@22200000`. | Confirmed |
| ASIC report | `device_id=0x000073a0`, `chip_rev=0x02600200`, family `147 (MGFX)`, `GEN=2`, `MOD=0x60`, `EVT=2`. | Confirmed |
| Compute/GFX IP | One GFX IP and one COMPUTE IP, both version `10.0`; DMA count was zero in the probe output. | Confirmed |
| Execution width/topology | `SE=1`, `SA/SE=2`, `CU_active=12`, `CU/SH=6`, `wave=32`, `RB=4`. | Confirmed for this capture |
| Addressing | VA offset `0x8000000`, maximum `0x800000000000`, alignment `0x1000`, GART page size `0x1000`. | Confirmed for this capture |
| Firmware | SGPU `2.23.0`, RTL CL `0x0004ea15`; ME `0x5`, MEC `0x4`, PFP `0x7`, RLC `0x1`; CE/MC/SDMA/SMC and RLC subcomponents reported zero. | Confirmed for this capture |
| Page faults | `faults=0` at the time of the probe. | Confirmed for this capture |
| Memory/VM probe | `GEM_CREATE`, CPU `mmap+touch`, `VA_MAP`, `VA_UNMAP`, and `GEM_CLOSE` succeeded. | Confirmed for this capture |
| Failed queries | DRM version failed with `errno=14`; `SGPU_KMD_VERSION` failed with `errno=22`. | Confirmed for this capture |
| Vulkan loader | System loader path `/system/lib64/libvulkan.so`, observed size 240,208 bytes. | Confirmed by report |
| Vendor ICD | `/vendor/lib64/hw/vulkan.samsung.so`, observed size 44,423,944 bytes. | Confirmed by report |
| Vulkan namespace result | Only `llvmpipe` was visible through the Termux-accessible loader; root plus temporary `Permissive` did not change it. | Confirmed for this environment |
| Direct ICD attempt | Linker namespace denied `/vendor/lib64/hw`; the process then segfaulted. | Confirmed for this approach |
| Security state | SELinux was restored to `Enforcing` after the experiment. | Confirmed by logs |

## Source-backed architecture

The Samsung source release contains `kernel/include/uapi/drm/sgpu_drm.h`, a Samsung SGPU driver under `kernel/drivers/gpu/drm/samsung/gpu/sgpu`, the Samsung platform Device Tree files `s5e9945-sgpu_common.dtsi` and `s5e9945-sgpu_evt0.dtsi`, Samsung IOMMU support, Samsung DMA-BUF heaps, and GPU register headers under `include/asic_reg`.

The UAPI header is AMDGPU-derived in naming and layout. It defines GEM creation, GEM mmap, contexts, BO lists, command submission, information queries, GEM VA operations, waits, VM, scheduler, and Samsung-specific instance/memory-profile operations. It defines GFX, COMPUTE, and DMA IP types; command-stream chunks for IBs, fences, dependencies, sync objects, BO handles, and timeline operations; and queries for device information, firmware, memory, registers, sensors, KMD version, and GPU page faults.

The source Makefile shows that the `sgpu` module compiles broad AMDGPU-derived subsystems, including `amdgpu_gem`, `amdgpu_cs`, `amdgpu_vm`, `amdgpu_ring`, `amdgpu_sync`, `amdgpu_sched`, GMC/MMHUB/GFX/SDMA blocks, firmware handling, Samsung DVFS/AFM/IFPO/debug/profiler pieces, and tracepoints. This is evidence of implementation material in the source release. It is not evidence that a user-space Mesa driver can use the ABI without understanding the Samsung modifications and runtime contract.

The s5e9945 Device Tree declares `sgpu@22200000`, compatible `samsung-sgpu,samsung-sgpu`, six register regions named `gpu`, `doorbell`, `debug`, `pwrctl`, `sysreg`, and `htu`, SGPU and GPU-AFM interrupts, `CHIP_VANGOGH_LITE`, a GPU power domain, and `dma-coherent`. The EVT0 include sets `chip_revision = <0x02600100>`; the observed device reports `0x02600200`, so revision-specific data must not be merged without an explicit correlation.

## Test classification

| Artifact | What actually happened | Correct class |
| --- | --- | --- |
| `sgpu_raw_probe.c` | Read-only DRM/KMD queries plus reversible 64 KiB BO/CPU map/VA map/unmap; no submission. | Bring-up probe and memory/VM smoke test |
| `probe_SM-S721B.txt` | Recorded identity, IP, firmware, page faults, and BO/VA results. | Raw probe output |
| `vk_exec_props.c` | Loader/instance/device selection, trivial pipeline creation, executable property/statistics/IR queries; no queue retrieval, dispatch, submit, or readback. | Vulkan pipeline metadata probe |
| `exec_props.txt` and `exec_props_root.txt` | Both report one `llvmpipe` device and no visible Samsung GPU. | Negative loader observation |
| Direct ICD experiment | `dlopen` path blocked by linker namespace and followed by SIGSEGV. | Discarded approach for this namespace |
| `FRIEND_PROBE.md` | Instructions and expected outputs, including a statement that no GPU work is submitted. | Test plan/initializer guidance |
| `vulkan.samsung.so` and `libdrm_sgpu.so` | Vendor binaries copied for offline analysis. | Reference binaries; not open project code |
| Samsung source archive | Source and build material available for inventory. | Source evidence; license review required |

## Negative results that remain useful

The two SGPU probe failures are preserved: DRM version `errno=14` and `SGPU_KMD_VERSION` `errno=22`. They close only those query paths under that exact invocation. They do not prove that DRM, KMD, or the GPU is unusable because the same run successfully returned device/IP/firmware/page-fault information and completed memory/VM operations.

The Vulkan result closes one specific path: the loader visible to the Termux process, even with root and temporary permissive SELinux, did not expose the Samsung device. It does not prove that the Samsung ICD is absent, that a production graphics process cannot load it, or that an external loader can never access it.

## Immediate next experiments

The safest next sequence is to preserve and hash all raw artifacts; inspect ICD dependencies and exported interfaces without loading it directly; identify the Android process, loader path, and namespace that successfully use the vendor ICD; and document the relationship between the public-looking UAPI and the Samsung driver implementation. Only after the Samsung device is genuinely visible to a supported loader should layers or a real queue/compute experiment be attempted.

A real compute milestone requires all of the following in one report: target-device proof, resource allocation and mapping, queue selection, command recording, queue submission, synchronization, and independent validation of the expected result. Until then, the project must not claim compute execution, ISA extraction, a compiler backend, or an independent Vulkan driver.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[3]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
