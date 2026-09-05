# Android Loader

**Status:** the failure boundary is confirmed; the production Samsung loading bridge is not yet identified.

The handset contains `/system/lib64/libvulkan.so` and `/vendor/lib64/hw/vulkan.samsung.so`. In the Termux process, opening the system loader created an instance and exposed only `llvmpipe` with vendor `0x10005`; vendor `0x144d` was not visible. Repeating from a root session with SELinux temporarily permissive produced the same result. Directly opening the vendor ICD path was rejected by the linker namespace and then ended in a segmentation fault.

| Component | Observed state | Confidence |
| --- | --- | --- |
| System loader | Present at `/system/lib64/libvulkan.so`. | Confirmed by supplied report |
| Vendor ICD | Present at `/vendor/lib64/hw/vulkan.samsung.so`. | Confirmed by supplied report |
| Termux loader result | `llvmpipe` only. | Confirmed for that process/context |
| Root effect | Did not change device visibility. | Confirmed for that experiment |
| SELinux effect | Temporary `Permissive` did not change visibility; restored to `Enforcing`. | Confirmed for that experiment |
| Direct ICD | Namespace denied the path. | Confirmed for that approach |

The defensible conclusion is a loader/namespace/ICD integration boundary. Root is not equivalent to linker-namespace membership, and the vendor ICD is not automatically a standalone `libvulkan.so`.

## Next safe observation

Identify which production Android process and namespace loads the vendor ICD, inspect dependencies and manifests without modifying `/system` or `/vendor`, and document the supported bridge. Only then should a user-space loader or layer be evaluated.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
