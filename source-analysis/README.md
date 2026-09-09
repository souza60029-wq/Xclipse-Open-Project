# Samsung Source Analysis

The Quick Share archive is an evidence package, not automatically an open-source dependency. The first operation is inventory, not compilation.

## Required inventory

Record the archive SHA-256, file count, compressed and uncompressed sizes, top-level directories, duplicate files, generated files, copyright notices, SPDX identifiers, license texts, and paths related to GPU/DRM, UAPI, Device Tree, firmware, IOMMU, memory management, queues, scheduling, reset, power, Vulkan HAL, manifests, linker namespaces, SELinux, and HWC.

For each relevant file, record its path, type, license/provenance, relevant symbols, relation to SM-S721B/Xclipse 940 (XO940 is the project name), and whether it is a public interface, reference implementation, generated artifact, or proprietary implementation detail.

## Legal boundary

The private status of this repository does not create a license. Until the license inventory is complete, Samsung source and firmware must remain reference material. New project code must not copy implementation text, proprietary headers, or generated binaries without a verified redistribution basis.

## Expected outputs

- `inventory.csv` with one row per archive member.
- `license-inventory.md` with evidence and unresolved notices.
- `kernel-paths.md` for DRM, UAPI, VM, scheduler, reset, and firmware paths.
- `vulkan-integration.md` for HAL, loader, manifest, namespace, SELinux, and HWC paths.
- `source-cross-reference.csv` mapping project questions to source files and evidence labels.
- `../reports/source-archive-initial-analysis.md` summarizing counts and high-value findings.
- `variant-device-trees.md` comparing the additional S7210/S721J/S721Q/S721U Device Tree packages from the outer `SM-S721B.zip` bundle.
- `../data/sm-s721b-bundle-manifest.txt` recording the outer and nested archive hashes.

## Quasar cross-reference

The [`quasar` branch](https://github.com/souza60029-wq/Xclipse-Open-Project/tree/quasar) contains the direct DRM probe, raw experiment capsules, reproduction guide and implementation roadmap. The `X940-001c` result identifies the DRM-reported layer on four nodes; it must be correlated with sysfs and the SGPU Device Tree before being interpreted as a physical GPU mapping.
