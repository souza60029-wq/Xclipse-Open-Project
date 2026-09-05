# ICD and Manifests

**Status:** the vendor ICD is confirmed in the production SurfaceFlinger process; standalone external-loader use and full Vulkan enumeration remain open.

The device report identifies `/vendor/lib64/hw/vulkan.samsung.so` at 44,423,944 bytes and `/system/lib64/libvulkan.so` at 240,208 bytes. The new Etapa 1 capture shows both libraries mapped inside `SurfaceFlinger` PID 995 while valid, together with FDs pointing to `/dev/dri/renderD128`. This is stronger than a file-existence check and establishes a real production loading path.

The vendor ELF is AArch64, stripped, Android NDK r26, with BuildID `7b6134ba45f561f28b006e07ef075b4d4c429bcd`. Its dependencies include Android/vendor libraries such as `libhardware.so`, `libsync.so`, `libsbwchelper.so`, and `android.hardware.graphics.mapper@4.0-impl-sgr.so`. These dependencies explain why copying the library into Termux or treating it as a standalone Mesa ICD is not a valid equivalence.

| Artifact | Meaning | Current status |
| --- | --- | --- |
| `vulkan.samsung.so` | Vendor Vulkan implementation mapped by SurfaceFlinger. | Production presence confirmed; external standalone use unproven. |
| `libvulkan.so` | Android/system loader mapped by SurfaceFlinger and visible to Termux. | Present; Termux selected llvmpipe. |
| SurfaceFlinger `renderD128` FDs | Production process reaches SGPU DRM. | Confirmed in valid snapshot. |
| Vulkan JSON | Standard external-loader manifest. | Not found in the searched paths. |
| OpenCL `samsung.icd` | OpenCL manifest, not Vulkan JSON. | Must not be classified as Vulkan manifest. |

The difference between the SurfaceFlinger and Termux mount namespaces is material. A successful production mapping does not grant Termux access, and exported Vulkan entry points do not prove that an external Mesa loader can call the library.

## References

[1]: ../reports/new-results-analysis.md "Analysis of the new results package"
[2]: ../reports/5-dois-meios-essenciais.md "Five essential discoveries"
[3]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
