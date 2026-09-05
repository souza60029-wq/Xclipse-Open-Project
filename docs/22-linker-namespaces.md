# Linker Namespaces

**Status:** one namespace boundary is confirmed; namespace identity and the production bridge remain to be mapped.

The direct ICD experiment attempted to open `/vendor/lib64/hw/vulkan.samsung.so` from the Termux process and received a linker warning that the library was not accessible for the namespace, followed by a segmentation fault. Opening `/system/lib64/libvulkan.so` did work, but it exposed only llvmpipe. A root shell and temporary SELinux `Permissive` state did not alter this result.

| Observation | Evidence label | Limit |
| --- | --- | --- |
| Vendor path is inaccessible to the Termux namespace. | Confirmed for this approach | Does not name the namespace or prove every process is blocked. |
| Root did not change loader visibility. | Confirmed for this experiment | Root does not imply linker-namespace membership. |
| System loader is callable. | Confirmed for this process | It may select different ICDs in another Android process. |

## Next safe work

Identify the production graphics process and its linker configuration using read-only inspection. Do not modify namespace configuration, `/system`, `/vendor`, manifests, or persistent properties. Do not use a segmentation fault as evidence of ICD incompatibility; it proves only that this loading approach is invalid in this namespace.

## References

[1]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
[2]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
