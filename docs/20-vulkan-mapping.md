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

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.

## Quasar: limite da identificação DRM

O nome `amdgpu` observado por `DRM_IOCTL_VERSION` em `renderD128` é uma pista de compatibilidade estrutural, não uma confirmação de ABI AMDGPU upstream, ISA AMD ou RADV. O mapeamento Vulkan deve continuar separado da identificação do driver DRM.

