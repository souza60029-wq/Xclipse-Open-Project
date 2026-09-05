# ICD and Manifests

**Status:** vendor ICD presence is confirmed; a usable manifest/loader path is not.

The device report identifies `/vendor/lib64/hw/vulkan.samsung.so` at 44,423,944 bytes and `/system/lib64/libvulkan.so` at 240,208 bytes. `readelf` reported instance-level Vulkan exports from the vendor library, including `vkCreateInstance` and enumeration functions. The same report did not find a standard Vulkan JSON manifest in the searched locations; it did find `/vendor/etc/Khronos/OpenCL/samsung.icd`, which is an OpenCL manifest and must not be treated as a Vulkan manifest.

| Artifact | Meaning | Current status |
| --- | --- | --- |
| `vulkan.samsung.so` | Vendor Vulkan implementation/ICD candidate. | Present; standalone usability unproven. |
| `libvulkan.so` | Android/system loader visible to the probe. | Present; selected llvmpipe in Termux. |
| `samsung.icd` | OpenCL ICD file. | Present; irrelevant as Vulkan JSON. |
| Vulkan JSON | Standard external-loader manifest. | Not found in searched paths. |

Exported Vulkan entry points do not prove that the library is a complete loader or that an external Mesa loader can call it. The next step is to identify the Android/HAL discovery path and ABI contract that production processes use.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
[3]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
