# Roadmap

The project advances by evidence, not by directory count. Each phase has an entry criterion and an exit artifact.

| Phase | Goal | Exit evidence |
| --- | --- | --- |
| 0. Governance and inventory | Identify device, revision, firmware, archive hash, licenses, and open questions. | `STATUS.md`, archive manifest, inventory, license review queue. |
| 1. Platform and kernel | Map Device Tree, DRM, UAPI, memory, queues, firmware, reset, and scheduler. | Reproducible platform-to-GPU boot map. |
| 2. Safe observability | Build read-only probes and reversible observations. | Raw outputs and reports with safety boundaries. |
| 3. Memory and MMU | Understand heaps, virtual addresses, page tables, permissions, cache, and faults. | Reproducible allocation and mapping evidence. |
| 4. Queues and command processor | Identify rings, packets, fences, doorbells, preemption, and recovery. | Evidence-backed packet hypotheses and decoder fixtures. |
| 5. Minimal compute | Execute one controlled operation and validate readback. | Target-device proof, submission, synchronization, and expected result. |
| 6. ISA and compiler | Correlate controlled shaders with binaries and instruction hypotheses. | At least one instruction form decoded with confidence. |
| 7. Vulkan mapping | Map device, memory, queue, compute, images, barriers, and presentation. | Minimal independently reproducible API path. |
| 8. Diagnostic layers | Add tracing and validation where the real ICD is visible. | Layer loaded without modifying the ICD. |
| 9. Independent driver | Implement the smallest useful open driver surface. | One real Vulkan feature on the target hardware. |
| 10. Validation and maintenance | Regression-test revisions and document limitations. | Versioned test suite, bug records, and maintained specifications. |

The current project is in Phase 0. The shared archive inventory is the immediate dependency for advancing.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
