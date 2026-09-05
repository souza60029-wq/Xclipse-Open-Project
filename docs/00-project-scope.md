# Project Scope and Terminology

## Purpose

Xclipse Open Project aims to make the hardware/software contract of Samsung Xclipse GPUs understandable to independent developers. The scope begins with documentation and reproducible observation. Implementation follows only where the evidence, legal status, and recovery model justify it.

The target is the Xclipse 940 associated with the Samsung SM-S721B, while XO940 is the internal name of the documentation project. The repository is designed so that later Xclipse generations and revisions can be represented without silently merging incompatible observations.

In this project, the **Device Tree** is the kernel-facing description of the Xclipse platform: register regions, interrupts, power domains, clocks, DMA/coherency, IOMMU relationships, reset dependencies, compatible strings, and revision-specific properties. It is not the repository directory tree and it is not the complete driver by itself. It is the platform contract that the SGPU driver consumes to bring the GPU into the DRM/runtime system.

## Technical vocabulary

The work combines GPU reverse engineering, open GPU documentation, driver bring-up, and open-source Vulkan driver development. A future Mesa integration would be a Mesa Vulkan driver backend. Transforming shader representations into Xclipse instructions is compiler-backend work, normally involving an intermediate representation such as NIR or LLVM. Describing kernel interfaces, memory, queues, and firmware is GPU hardware enablement and DRM driver bring-up.

“Turnip” is the name of a Vulkan driver for Qualcomm Adreno GPUs. “RADV” is a Vulkan driver for AMD Radeon GPUs in Mesa. This project uses “Turnip-like” only to describe the general category of an open Vulkan driver for Xclipse; it does not imply code reuse, architectural equivalence, or compatibility with either driver.

## In scope

The project covers hardware identification; chip revisions; Device Tree and platform integration; DRM and UAPI; GEM, buffer objects, DMA-BUF, IOMMU, virtual memory and faults; scheduler, fences, sync objects and reset; GFX, compute and DMA queues; firmware references; clocks, power, OPP and thermal behavior; Android loader namespaces, ICD manifests, SELinux and HWC integration; command packets; register and shader captures; preliminary ISA; compiler lowering; Vulkan mapping; diagnostic layers; and validation across revisions.

## Out of scope until separately approved

The project does not initially promise game compatibility, RADV or Turnip compatibility, firmware replacement, device flashing, unsafe register writes, opaque command-buffer replay, publication of proprietary Samsung code, or a complete independent driver. Those may remain impossible, unsafe, or legally restricted even if the documentation becomes extensive.

## Evidence rule

A fact is not elevated because it appears in a test directory or because a binary returns success. The project distinguishes setup from execution:

| Artifact or action | What it can establish | What it cannot establish by itself |
| --- | --- | --- |
| Test initializer, fixture setup, or environment check | The test environment was prepared. | That a GPU operation ran or produced a correct result. |
| Vulkan instance/device enumeration | A loader path exposed one or more API objects. | That custom queue submission, shader execution, rendering, or presentation works. |
| Capability query | The implementation reported a property or extension. | That the feature is correctly executable under all relevant states. |
| Buffer allocation and mapping | An allocation path and CPU visibility worked. | That the GPU can access the buffer or that synchronization is correct. |
| Command recording | An API accepted command construction. | That the command was submitted, executed, or completed. |
| Queue submission call | The API accepted a submission request. | That firmware executed it or that the result is correct. |
| Fence/semaphore completion | A synchronization object reached a state. | That the intended GPU work, rather than an unrelated path, caused it. |
| Readback with expected value | A specific end-to-end operation passed. | That all formats, queues, revisions, or workloads are supported. |
| Shader binary capture | The implementation emitted a binary for an input. | That the binary's ISA is understood or portable. |

## Success is layered

A successful project milestone must identify the exact layer that passed. Platform and loader success precede kernel and memory success. Kernel and memory success precede queue and compute success. Compute success precedes ISA and compiler claims. Vulkan and layer work must be tested in an environment where the relevant Samsung GPU is genuinely visible.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
