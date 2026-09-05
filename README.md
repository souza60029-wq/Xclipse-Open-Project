# Xclipse Open Project

**Xclipse Open Project** is an evidence-driven effort to document Samsung Xclipse GPU platforms and, only when the evidence supports it, build open analysis tools, compiler components, diagnostic layers, and an experimental Vulkan driver.

The first target is the **Samsung Xclipse 940** found in the **SM-S721B**, with the internal target name **XO940**. The repository is intentionally conservative: it separates direct observations, source-backed facts, reproducible experiments, hypotheses, and failed approaches. A documentation milestone is not presented as a driver milestone.

> **Current conclusion:** the project has a real-device laboratory target, a hashed and indexed Samsung source/evidence archive, a confirmed SGPU DRM/memory bring-up path, and an unresolved Android loader/namespace boundary. It does **not** yet demonstrate an independent Vulkan driver, custom command submission, compute dispatch/readback, a decoded ISA, or a compiler backend.

## Evidence-first policy

Every non-trivial claim must point to an artifact, a source path, or a reproducible experiment. Raw outputs are preserved without cosmetic editing. Interpretations live in separate documents. Test names must describe what was actually executed; a probe initializer, a capability query, or a test harness that never submits GPU work is not a rendering or compute test.

The project uses five evidence labels:

| Label | Meaning |
| --- | --- |
| **Confirmed** | Observed directly and reproduced, or supported by a clear public source. |
| **Confirmed by source** | Found in Samsung source, kernel documentation, or a verifiable symbol/path. |
| **Probable** | Inferred from multiple observations but not directly tested. |
| **Hypothesis** | A working possibility that still requires an experiment. |
| **Discarded** | The specific approach was tested and failed; this does not prove every alternative fails. |

## What is currently known

The Samsung Vulkan ICD is reported at `/vendor/lib64/hw/vulkan.samsung.so`, and the system loader is reported at `/system/lib64/libvulkan.so`. The original Vulkan probe, when run from the Termux namespace, exposed only `llvmpipe` with vendor `0x10005`; the Samsung GPU with vendor `0x144d` was not visible in that namespace. The same visibility result was observed in a root session with SELinux temporarily permissive. Directly opening the Samsung ICD from the Termux environment was blocked by the linker namespace and ended in a segmentation fault. The defensible interpretation is an Android loader/namespace/ICD integration problem, not proof that the vendor driver is absent.

The SGPU path is independently visible through DRM: `/dev/dri/renderD128` is bound to the `sgpu` platform driver, while `/dev/dri/renderD129` belongs to `exynos-drm`. The supplied SGPU probe completed a 64 KiB GTT allocation, CPU map/touch, VA map/unmap, and close; its source explicitly submits no GPU work. The Vulkan probe creates a pipeline candidate but has no queue, dispatch, submit, or readback.

These observations are recorded as initial project evidence, not as a claim that a custom driver already works.

## Repository map

The repository is organized by the dependency order of the work:

- `docs/` contains the platform, kernel, memory, MMU, command processor, ISA, Vulkan, Android, and validation specifications.
- `source-analysis/` contains inventories and cross-references for the Samsung source archive. The archive itself is not committed until licensing and size constraints are understood.
- `data/` contains small, reproducible metadata and sanitized fixtures. Sensitive or raw device captures remain local by default.
- `tools/` contains safe probes, parsers, decoders, and reproducibility helpers.
- `tests/` distinguishes harness initialization, observation, and actual GPU work. A test is only a test of a capability when it executes and validates that capability.
- `compiler/`, `driver/`, `layers/`, and `android/` are implementation areas that remain experimental until their entry criteria are met.
- `reports/` stores experiment reports, failures, comparisons, and milestone decisions.
- `artifacts/` stores the downloadable principal PDF and the separate PDF-only ramificação técnica. The principal PDF documents the chip paths and tests; the ramificação contains only the mapped Xclipse/kernel/GPU/DRM/Vulkan/OpenCL structure.

## Investigation sequence

The recommended order is: governance and inventory; platform and kernel; safe observability; memory and MMU; queues and command processor; minimal compute; ISA and compiler; Vulkan mapping; diagnostic layers; independent driver; validation and maintenance.

This order is deliberate. Vulkan API work cannot substitute for understanding memory allocation, virtual memory, queue submission, synchronization, firmware, reset, and recovery.

## Immediate next step

Complete file-level license/provenance review for the Samsung source and vendor binaries, then map the production Android process and linker namespace that loads the Samsung ICD. Only after that should the project design a minimal queue/submit/readback experiment. Do not copy proprietary code or firmware into new implementation files before a license review.

## Safety and provenance

This repository is private at creation time. It does not grant permission to redistribute Samsung code or firmware. See `NOTICE.md`, `LICENSE`, `SECURITY.md`, and `CONTRIBUTING.md` before adding source or device artifacts.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
[3]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
