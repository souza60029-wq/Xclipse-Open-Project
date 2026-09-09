# Android Loader

**Status:** the production Samsung loading path is now observed; detailed Vulkan enumeration from that process remains open.

The supplied Etapa 1 evidence captured `SurfaceFlinger` with PID 995 while it was alive. The process mapped both `/system/lib64/libvulkan.so` and `/vendor/lib64/hw/vulkan.samsung.so`, and held file descriptors pointing to `/dev/dri/renderD128`. Its mount namespace was `mnt:[4026535441]`; the observed Termux process used `mnt:[4026535972]`. This explains why Termux exposed only `llvmpipe` without implying that the Samsung ICD is absent.

The first attempt to inspect PID 995 was invalid because the process had already exited. `/proc/995/status`, `/proc/995/maps`, and its namespace directory were missing in that snapshot. A later, time-consistent capture supplied the valid maps and DRM file descriptors. The project must retain this distinction.

| Component | Observed state | Confidence |
| --- | --- | --- |
| System loader | `/system/lib64/libvulkan.so` mapped in SurfaceFlinger. | Confirmed in a valid process snapshot |
| Vendor ICD | `/vendor/lib64/hw/vulkan.samsung.so` mapped in SurfaceFlinger. | Confirmed in a valid process snapshot |
| Production DRM path | SurfaceFlinger FDs point to `/dev/dri/renderD128`. | Confirmed in a valid process snapshot |
| Termux loader result | `llvmpipe`, vendor `0x10005`. | Confirmed for Termux only |
| Samsung Vulkan enumeration | A complete `vkEnumeratePhysicalDevices` capture from SurfaceFlinger is not yet present. | Open |
| Direct ICD loading | Termux linker namespace rejected the vendor path and the attempt ended in a crash. | Confirmed for that approach |

The defensible conclusion is now stronger than “vendor library exists”: a production Android process maps the Samsung ICD and opens the SGPU render node. It is still not evidence that an external Mesa loader can call the vendor library, nor that Termux can join the production namespace. Root and temporary SELinux changes do not substitute for namespace membership.

## Next safe observation

Capture a supported process while it enumerates Vulkan devices and records vendor/device IDs, extensions, and the relation to the graphics service. Do not replace `/system` or `/vendor` libraries and do not treat a dead-PID snapshot as evidence.

## References

[1]: ../reports/new-results-analysis.md "Analysis of the new results package"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
[3]: ../reports/5-dois-meios-essenciais.md "Five essential discoveries"

## Quasar: condição de reprodução no Android

O workspace Quasar registrou que o armazenamento compartilhado pode ser montado com `noexec`. Probes devem ser compilados e executados em uma área de trabalho executável e somente depois arquivados em `Download/Quasar`.

