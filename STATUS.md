# Project Status

**Status date:** 2026-09-05  
**Initial target:** Samsung Xclipse 940 / SM-S721B / XO940  
**Repository posture:** private, evidence collection and documentation phase

## Executive status

The project is at **Phase 0: governance and inventory**, with initial observations from the attached plan. The Quick Share package is being treated as an external evidence set that must be hashed, inventoried, and license-reviewed before any file is treated as reusable implementation material.

The project is not yet at the driver bring-up phase. No statement in this file should be read as proof of a working independent ICD, custom queue submission, compute execution, ISA decoding, compiler lowering, or Mesa integration.

## Evidence ledger

| Area | Current statement | Evidence class | What is still required |
| --- | --- | --- | --- |
| Device | SM-S721B is the first laboratory target. | Confirmed by project plan | Device build, board identifiers, revision, and reproducible properties. |
| Target GPU | Xclipse 940 / XO940 is the initial target name. | Confirmed by project plan | Correlate target name with device revision and source identifiers. |
| Vendor ICD path | `/vendor/lib64/hw/vulkan.samsung.so` is reported. | Confirmed by observation in plan | Preserve command output and verify on the device. |
| Android loader path | `/system/lib64/libvulkan.so` is reported. | Confirmed by observation in plan | Preserve command output and record build/ABI context. |
| Termux Vulkan probe | Only `llvmpipe`, vendor `0x10005`, was visible. | Confirmed by observation in plan | Reproduce with exact binary, environment, loader search paths, and output. |
| Samsung GPU enumeration | Vendor `0x144d` was not visible in that namespace. | Confirmed by observation in plan | Test from a correctly configured Android process and compare namespaces. |
| Root | KernelSU root was available; a root session was confirmed. | Confirmed by observation in plan | Record exact root method and avoid treating root as a namespace bypass. |
| SELinux | Permissive was used during an experiment and Enforcing was restored. | Confirmed by observation in plan | Record policy, timestamps, and safe recovery procedure. |
| Direct ICD loading | Direct `dlopen` from the Termux context was blocked by the linker namespace and ended in SIGSEGV. | Confirmed by observation in plan | Capture the exact error and test supported loader/manifest paths. |
| Kernel/UAPI | Samsung kernel source is available for analysis. | Confirmed by project plan | Inventory Kconfig, DRM, IOCTL/UAPI, VM, scheduler, reset, firmware, and DT paths. |
| Custom GPU work | No custom submission or independent driver is demonstrated. | Confirmed negative status | Build a reversible minimal experiment only after memory, VM, queue, and recovery are understood. |
| ISA/compiler | No decoded instruction format or compiler backend is demonstrated. | Confirmed negative status | Collect controlled shaders and correlate binaries with disassembly/IR. |

## Entry criteria for the next phase

Phase 0 is complete only when the source archive has a cryptographic hash, a complete file inventory, a license inventory, a device/build identity, a firmware inventory, and a list of open questions. The next phase is platform/kernel mapping, not layer development.

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
