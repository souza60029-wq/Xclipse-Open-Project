# Project Status

**Status date:** 2026-09-17
**Internal project name:** XO940
**Initial target:** Samsung Xclipse 940 / SM-S721B
**Repository posture:** private technical documentation and reproducibility research

## Executive status

The project is in the **platform mapping and reproducibility phase**. The current evidence covers Device Tree, SGPU/DRM, memory, VM, IOMMU, scheduler, IB, fences, Android namespaces and vendor process paths. The latest package adds a larger Xclipse log collection, additional Samsung source-package references, Device Tree variants and a separate XLIA collection.

The raw archives remain outside Git. Public files contain sanitized technical derivatives, maps, classifications and provenance. XLIA, Exynos NPU and unrelated Android subsystem material are kept separate from the Xclipse GPU documentation.

## Evidence ledger

| Area | Current statement | Evidence class | Open requirement |
| --- | --- | --- | --- |
| Device | SM-S721B, platform `erd9945/s5e9945`. | Confirmed from device records | Correlate later builds and revisions. |
| Target GPU | Xclipse 940, MGFX 147, device `0x73a0`. | Confirmed for reference capture | Keep other variants separate. |
| Device Tree | `/sgpu@22200000`, compatible Samsung SGPU, G3DCORE, MMIO, IRQ, power, DMA and IOMMU relationships. | Confirmed and source-correlated in part | Close clocks, reset, revision and sequencing. |
| DRM topology | `card0`/`renderD128` report `amdgpu`; `card1`/`renderD129` report `exynos-drmdpu`. | Direct ioctl observation | Correlate each node through sysfs and platform device. |
| Memory/VM | BO creation, CPU mapping, VA mapping and VM activity observed. | Confirmed for the capture | Prove GPU access, residency, cache and readback. |
| Submission | Scheduler and IB activity observed in the system path; a specific CS input was accepted by the KMD. | Partial positive | Correlate controlled job, fence, effect and readback. |
| Compute | Vendor OpenCL surface and runtime consumers identified. | Confirmed surface / execution partial | Capture controlled dispatch and output. |
| Android path | System loader, vendor libraries, SurfaceFlinger and render node relationships observed. | Confirmed in supported process | Map supported loader bridge and namespace boundaries. |
| Shader/ISA | Compiler and encoding indications exist in sources and metadata. | Static indication | Correlate controlled inputs with native output. |
| Variant matrix | S721B, S721U, S7210, S721Q and S721J source-package references are present. | Confirmed by package inventory | Compare GPU-specific properties and revisions. |
| XLIA | Separate Android, Vulkan, power, thermal, memory and session collections exist. | Separate project evidence | Keep outside the Xclipse GPU corpus. |
| NPU/NNAPI | ENN accepted and executed selected elementwise, fully connected and quantized graphs; several attention and multi-output forms were rejected. | Confirmed per workload with checksum or explicit compiler status | Build a sanitized NNAPI reproduction matrix and isolate thermal telemetry. |

## NPU branch

The new collection adds a separate Exynos NPU/NNAPI branch. It includes ENN device selection, model compilation status, operation support queries, checksum validation, CPU baselines and sustained timing. It is documented as a separate accelerator path and is not treated as GPU execution.

The strongest current NPU evidence is a quantized four-layer `FULLY_CONNECTED` graph with equal output checksum and an observed ENN timing advantage over the NNAPI CPU reference. Other workloads, including floating-point matrix multiplication, softmax and attention, show either CPU advantage or compilation limits.

## Current technical state

The stable operational chain is:

```text
Device Tree / power / clocks / reset
        ↓
SGPU kernel driver / DRM UAPI
        ↓
GEM/BO + VM/PTE/PDE + IOMMU
        ↓
context + BO_LIST + CS + IB
        ↓
scheduler + ring + command processor
        ↓
GPU GFX/COMPUTE
        ↓
fence / syncobj / retire
        ↓
readback or presentation
```

The chain is a map of dependencies, not a claim that every stage has been independently reproduced.

## Latest collection boundaries

The package received on 2026-09-17 has SHA-256 `bd81f26cee79176805c983e639f3d341c48f01cf52680362c2de394818b4aec1`. It contains Xclipse logs, Samsung source-package archives, historical implementation material, XLIA collections and Exynos NPU experiments. The public documentation uses only sanitized technical findings from the Xclipse set.

The Samsung source-package archive is treated as a reference package. Its presence does not by itself grant redistribution rights for source, firmware, generated files or vendor libraries. The raw package remains external evidence.

## Open questions

1. Which exact driver and platform-device links correspond to every DRM node?
2. Which UAPI fields, flags, alignments and permissions are stable across Xclipse variants?
3. Which BO, VM and IOMMU operations are required for a controlled GPU write and readback?
4. Which fence and retire events can be correlated without relying on unrelated system workloads?
5. Which Android namespaces and loader interfaces are reproducible across devices?
6. Which shader, packet and compiler fields are stable under controlled inputs?
7. Which Xclipse properties are shared by S721B, S721U, S7210, S721Q and S721J?

## Publication rule

The release is updated from sanitized technical documentation and maps. Raw logs, captures, dumps, personal Android state, credentials, firmware, vendor libraries, binaries and source archives are not release assets.

## References

[1]: https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06 "Public Xclipse documentation release"
[2]: ../docs/validation-and-evidence.md "Validation and evidence protocol"

[1] [2]
