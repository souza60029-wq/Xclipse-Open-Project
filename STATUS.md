# Project Status

**Status date:** 2026-09-18
**Internal project name:** XO940
**Initial target:** Samsung Xclipse 940 / SM-S721B
**Repository posture:** private technical documentation and reproducibility research

## Executive status

The project is in the **platform mapping and reproducibility phase**, with a separately validated rootless NNAPI/ENN branch. The current evidence covers Device Tree, SGPU/DRM, memory, VM, IOMMU, scheduler, IB, fences, Android namespaces, vendor process paths and a public NNAPI `IDevice/enn` execution path.

The raw archives remain outside Git. Public files contain sanitized technical derivatives, maps, classifications and provenance. NPU and unrelated Android subsystem material remain separate from the Xclipse GPU execution path.

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
| NPU/NNAPI | A no-root process discovered `android.hardware.neuralnetworks.IDevice/enn`, compiled a graph and executed `SOFTMAX` with exact output match. INT8 `FULLY_CONNECTED` remains validated; `BATCH_MATMUL` remains rejected. | Confirmed execution for the reproduced graphs | Build a sanitized NNAPI reproduction matrix for the accelerator branch. |
| ENN vendor AIDL | `vendor.samsung_slsi.hardware.enn_aidl.IEnnInterfaceAidl/default` was not found by the comparison probe, even with root. | Negative result for the probe | Determine registration/namespace conditions without assuming direct vendor access. |
| NPU endpoint | `/dev/vertex10` maps to `exynos-npu` and `/npu_exynos`; common UID receives permission denied and root probes returned invalid-argument/bad-address for tested ioctls. | Confirmed endpoint; direct API not reproduced | Do not use direct vertex access in the rootless app path. |

## NPU branch

The new collection adds a separately validated Exynos NPU/NNAPI branch. It includes ENN device selection, public Binder discovery, no-root model compilation, operation support queries, checksum validation, CPU baselines, sustained timing and endpoint permissions. It is documented as a separate accelerator path and is not treated as GPU execution.

The strongest current NPU evidence is the no-root `SOFTMAX` execution: the process found `IDevice/enn`, compiled the graph, executed it and received a maximum numerical difference of `0.000000`. The quantized four-layer `FULLY_CONNECTED` graph remains the strongest performance result, with equal output checksum and an ENN timing advantage over the NNAPI CPU reference. `SOFTMAX` is now classified as functionally supported for the reproduced graph but slow in the older 1024-element benchmark. `BATCH_MATMUL` and the direct vendor/vertex routes remain blocked for the tested forms.

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

The NPU package received on 2026-09-18 has SHA-256 `873a0f8d3ff4d8e9a4168aafab0349f5becfa539cfc9b03d8a7033e3acb08af9`. It contains the earlier NPU benchmarks plus collection items for services, interfaces, permissions, endpoints, root comparison, NNAPI HAL execution and Device Tree/sysfs. The public documentation uses only sanitized technical findings.

The Samsung source-package archive is treated as a reference package. Its presence does not by itself grant redistribution rights for source, firmware, generated files or vendor libraries. The raw package remains external evidence.

## Open questions

1. Which exact driver and platform-device links correspond to every DRM node?
2. Which UAPI fields, flags, alignments and permissions are stable across Xclipse variants?
3. Which BO, VM and IOMMU operations are required for a controlled GPU write and readback?
4. Which fence and retire events can be correlated without relying on unrelated system workloads?
5. Which Android namespaces and loader interfaces are reproducible across devices?
6. Which shader, packet and compiler fields are stable under controlled inputs?
7. Which Xclipse properties are shared by S721B, S721U, S7210, S721Q and S721J?
8. Which larger accelerator-shaped subgraphs are accepted by `IDevice/enn` through a normal app UID?
9. Can Burst, persistent memory and QKV concatenation reduce NNAPI overhead without changing numerical output?
10. Under which system state, if any, is the vendor ENN AIDL service registered for an ordinary client?

## Publication rule

The release is updated from sanitized technical documentation and maps. Raw logs, captures, dumps, personal Android state, credentials, firmware, vendor libraries, binaries and source archives are not release assets.

## References

[1]: https://github.com/souza60029-wq/Xclipse-Open-Project/releases/tag/documentation-2026-09-06 "Public Xclipse documentation release"
[2]: ../docs/validation-and-evidence.md "Validation and evidence protocol"
[3]: ../docs/30-npu-enn-rootless.md "NPU/ENN rootless evidence"

[1] [2] [3]
