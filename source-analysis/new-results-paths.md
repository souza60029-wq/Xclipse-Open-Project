# New results technical paths

The second results package strengthens the technical structure map without redistributing Samsung binaries. The paths below are evidence pointers derived from the supplied reports and runtime captures.

| Domain | Path or component | Evidence interpretation |
| --- | --- | --- |
| Platform | `/sys/devices/platform/22200000.sgpu` | Runtime SGPU platform device. |
| Device Tree | `/sgpu@22200000`, `samsung-sgpu,samsung-sgpu` | Runtime compatible and platform identity. |
| DRM | `/dev/dri/card0`, `/dev/dri/renderD128` | SGPU node reached by SurfaceFlinger and Photo Remaster service. |
| Display | `/dev/dri/card1`, `/dev/dri/renderD129` | Separate Exynos display path. |
| Android loader | `/system/lib64/libvulkan.so` | System loader mapped in the valid SurfaceFlinger snapshot. |
| Vendor Vulkan | `/vendor/lib64/hw/vulkan.samsung.so` | Vendor ICD mapped in SurfaceFlinger; standalone external use remains open. |
| Vendor DRM | `/vendor/lib64/libdrm_sgpu.so` | Vendor bridge with BO, VA, context, submit, fence and query interfaces visible in the inventory. |
| Vendor OpenCL | `/vendor/lib64/libOpenCL.so`, `/vendor/lib64/libSGPUOpenCL.so` | Loaded by the dedicated Photo Remaster service; API capability is not a project-controlled execution trace. |
| Kernel UAPI | `kernel/include/uapi/drm/sgpu_drm.h` | DRM contract for GEM, VM, contexts, IBs, fences, sync and SGPU extensions. |
| Kernel driver | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/` | SGPU implementation path for device, memory, queues, firmware and scheduling. |
| IOMMU | `kernel/drivers/iommu/samsung/` | Samsung IOMMU support requiring correlation with SGPU VA mapping. |
| DMA-BUF | `kernel/drivers/dma-buf/heaps/samsung/` | Android memory ownership and secure-buffer path. |
| Shader/compiler | Static strings in `vulkan.samsung.so` | Strong vocabulary for SPIR-V, opcode handling, emitters and encoders; not native ISA proof. |
| Production processes | `SurfaceFlinger`; `com.samsung.android.photoremasterservice:photoremasterservice` | Processes observed with vendor libraries and SGPU FDs. |

The technical branch PDF is a derived map of these paths. It does not claim that every path is open source, built into the tested image, or exercised by the project.

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
