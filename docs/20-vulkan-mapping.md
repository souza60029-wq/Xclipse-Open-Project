# Vulkan Mapping

**Status:** loader observation and pipeline metadata probe documented; independent Vulkan execution not demonstrated.

The supplied Vulkan probe uses no SDK headers. It loads `libvulkan.so`, creates a `VkInstance`, enumerates physical devices, selects vendor `0x144d` if visible, searches for `VK_KHR_pipeline_executable_properties`, creates a minimal device and compute pipeline from embedded SPIR-V, and queries executable properties, statistics, and internal representations.

The probe does **not** retrieve a device queue, record a command buffer, call `vkCmdDispatch` or `vkCmdDraw`, submit work, wait for a fence, or read back a result. Consequently, `compute pipeline ok` means that pipeline creation returned success; it does not mean that a compute workload executed.

| Vulkan layer | Supplied evidence | Current interpretation |
| --- | --- | --- |
| Loader open | `libvulkan opened` | The process opened the loader visible to its namespace. |
| Instance | `instance ok` | Instance creation worked for that loader. |
| Physical devices | One device: `llvmpipe`, vendor `0x10005`. | Software Vulkan was visible. |
| Samsung selection | `0x144d` not visible. | The observed process did not reach the Samsung ICD. |
| Pipeline executable extension | Not reached on the Samsung path. | Availability remains unproven through the correct bridge. |
| Queue/dispatch/readback | Not present in source. | No Vulkan compute execution claim is allowed. |

## Mapping priorities

Once the Android loader path is understood, the first executable Vulkan milestone should be device selection, queue retrieval, a bounded buffer, a minimal compute dispatch, synchronization, and validated readback. Images, textures, rendering, presentation, descriptors, pipeline cache, and layers should follow from evidence rather than from the existence of API entry points.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
