# Shader ISA

**Status:** no Xclipse instruction format is decoded with confidence.

The supplied Vulkan probe was designed to query `VK_KHR_pipeline_executable_properties`, including executable statistics and internal representations, from a minimal compute pipeline. The observed loader exposed only llvmpipe, so the Samsung UMD path was never reached by that probe. No ISA text, IR blob, or instruction capture from the Xclipse device is present in the evidence set.

The source release contains GPU register headers, firmware handling, and SGPU implementation material. Those paths are useful context but do not constitute a shader disassembler or a proof of user-space ISA availability.

| Evidence | Current interpretation |
| --- | --- |
| Embedded SPIR-V in the Vulkan probe | Controlled input exists; no Samsung output was captured. |
| `VK_KHR_pipeline_executable_properties` calls in source | A metadata route was attempted in code. |
| `llvmpipe` result | The observed output belongs to software Vulkan, not Xclipse. |
| Register and firmware headers | Source evidence requiring revision/license correlation. |

## Required next evidence

First obtain a real Samsung `VkPhysicalDevice` through the supported Android loader bridge. Then compare controlled shaders, record binary sizes and hashes, and separate any textual representation from compiler diagnostics or non-ISA metadata.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
