# Roadmap

The project advances by evidence, not by directory count. Each phase has an entry criterion and an exit artifact. The current order prioritizes the Device Tree and the platform contract before any unsafe or opaque GPU submission.

| Phase | Goal | Exit evidence |
| --- | --- | --- |
| 0. Governance and inventory | Identify device, revision, firmware, archive hash, licenses, and open questions. | `STATUS.md`, archive manifests, inventory, and license review queue. |
| 1. Device Tree and platform | Map SGPU nodes, register regions, interrupts, power domains, clocks, DVFS, resets, IOMMU links, and revision differences. | Reproducible `Device Tree → kernel SGPU → platform resources` map. |
| 2. Android vendor path | Identify production processes, linker namespaces, HAL/manifests, library dependencies, DRM permissions, SELinux domains, and vendor ABI. | Reproducible process-to-ICD-to-DRM path without replacing system/vendor files. |
| 3. Memory and protection | Understand GEM/TTM, BO, DMA-BUF, heaps, IOMMU, VA, cache, secure buffers, residency, and page faults. | Reproducible allocation/mapping lifecycle with explicit permissions and recovery boundaries. |
| 4. Firmware, queues, and recovery | Map firmware load order, GFX/COMPUTE/DMA IPs, rings, IBs, doorbells, contexts, fences, syncobjs, reset and timeout behavior. | Evidence-backed queue/submission model from a real vendor workload. |
| 5. Controlled compute | Observe one supported operation with known input, synchronization, and independently validated output. | Target-device proof of execution and readback; no opaque submit assumption. |
| 6. Shader binary and ISA | Correlate controlled SPIR-V/OpenCL input, produced/consumed binary, compiler metadata, and runtime behavior. | At least one instruction or encoding form decoded with confidence. |
| 7. Vulkan mapping | Map device, memory, queue, compute, images, barriers, and presentation after the supported path is understood. | Minimal independently reproducible API path. |
| 8. Diagnostic layers | Add tracing and validation where the real ICD is visible. | Layer loaded without modifying the ICD. |
| 9. Independent driver | Implement the smallest useful open driver surface. | One real Vulkan feature on the target hardware. |
| 10. Validation and maintenance | Regression-test revisions and document limitations. | Versioned test suite, bug records, and maintained specifications. |

## Current operating split

The five current priorities are defined in `reports/5-dois-meios-essenciais.md`. The first five quick discoveries are deliberately read-only or source-backed: Device Tree comparison, runtime inventory, library/ABI inventory, source-symbol indexing, and confidence-labeled ICD analysis.

The project is currently between Phase 0 and Phase 1. The archive and device identity are established; the next exit artifact is the complete Device Tree/platform map. A compute test must not jump ahead of the platform, memory, vendor-path, and recovery prerequisites.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
