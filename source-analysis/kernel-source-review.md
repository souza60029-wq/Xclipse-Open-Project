# Samsung Kernel Source Review

This report is generated from the Samsung source archive supplied by the project owner. It records source paths and text matches; presence in source is not proof that a path is built into the SM-S721B image or that a symbol has the runtime behavior expected by an open driver.

- Relevant kernel files indexed: 649
- Samsung GPU subtree files: 552
- Platform tree files: 13275

## High-value source areas

| Area | Path exists | File count |
| --- | --- | ---: |
| GPU/DRM implementation | `kernel/drivers/gpu/drm/samsung/gpu` | 0 |
| SGPU implementation | `kernel/drivers/gpu/drm/samsung/gpu/sgpu` | 0 |
| Register definitions | `kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg` | 0 |
| Samsung UAPI candidates | `kernel/include/uapi/drm` | 0 |
| IOMMU | `kernel/drivers/iommu/samsung` | 0 |
| DMA-BUF heaps | `kernel/drivers/dma-buf/heaps/samsung` | 0 |
| Device Tree candidates | `kernel/arch/arm64/boot/dts` | 0 |

## Search signals

### sgpu

Found 2726 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e8845-sgpu.dtsi:3` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e8845-sgpu.dtsi:20` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e8845-sgpu.dtsi:21` — `sgpu: sgpu@10E00000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e8845-sgpu.dtsi:25` — `compatible = "samsung-sgpu,samsung-sgpu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e8845-sgpu.dtsi:36` — `interrupt-names =  "SGPU", "GPU-AFM";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:3` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:18` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:19` — `sgpu: sgpu@16E00000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:23` — `compatible = "samsung-sgpu,samsung-sgpu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:31` — `interrupt-names =  "SGPU";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9925-sgpu.dtsi:32` — `memory-region = <&sgpu_rmem>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9935-sgpu.dtsi:3` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9935-sgpu.dtsi:20` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9935-sgpu.dtsi:21` — `sgpu: sgpu@16E00000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9935-sgpu.dtsi:25` — `compatible = "samsung-sgpu,samsung-sgpu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9935-sgpu.dtsi:37` — `interrupt-names =  "SGPU", "GPU-AFM";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-rmem.dtsi:51` — `sgpu_rmem: sgpu_rmem {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-rmem.dtsi:52` — `compatible = "exynos,sgpu_rmem";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu.dtsi:2` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu.dtsi:12` — `#include "s5e9945-sgpu_common.dtsi"`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu.dtsi:15` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu.dtsi:16` — `sgpu: sgpu@22200000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi:2` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi:19` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi:20` — `sgpu: sgpu@22200000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi:24` — `compatible = "samsung-sgpu,samsung-sgpu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi:36` — `interrupt-names =  "SGPU", "GPU-AFM";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi:2` — `* SAMSUNG SGPU device tree sourceA`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi:12` — `#include "s5e9945-sgpu_common.dtsi"`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi:15` — `/* sgpu */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi:16` — `sgpu: sgpu@22200000 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-thermal.dtsi:448` — `cooling-device = <&sgpu 0 0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945.dts:29` — `#include "s5e9945-sgpu.dtsi"`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945.dts:114` — `bootargs = "console=ram printk.devkmsg=on clocksource=arch_sys_counter clk_ignore_unused firmware_class.path=/vendor/firmware rcupdate.rcu_expedited=1 swiotlb=noforce loop.max_part=7 kvm-arm.protected_modules=exynos-hypervisor ttm.pages_lim`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945_evt0.dts:29` — `#include "s5e9945-sgpu_evt0.dtsi"`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/atombios.h:4515` — `USHORT    usGPUObjectId;                                 //GPU ID`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/atombios.h:4524` — `USHORT    usGPUObjectId;                                 //GPU ID`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/atombios.h:6164` — `USHORT usGPUReservedSysMemSize;`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/atombios.h:6303` — `usGPUReservedSysMemSize:          Reserved system memory size for ACP engine in APU GNB, units in MB. 0/2/4MB based on CMOS options, current default could be 0MB. KV only, not on KB.`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/atombios.h:6382` — `USHORT usGPUReservedSysMemSize;`
- [truncated; 2686 additional matches]

### xclipse

Found 1 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/drivers/gpu/drm/samsung/gpu/sgpu/exynos_gpu_interface.c:755` — `count = scnprintf(buf, PAGE_SIZE, "Samsung Xclipse %s\n", product);`

### amdgpu_abi

Found 13602 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/amd_pcie.h:43` — `#define AMDGPU_DEFAULT_PCIE_GEN_MASK (CAIL_PCIE_LINK_SPEED_SUPPORT_GEN1 \`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/amd_pcie.h:60` — `#define AMDGPU_DEFAULT_PCIE_MLW_MASK (CAIL_PCIE_LINK_WIDTH_SUPPORT_X1 \`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/amd_shared.h:57` — `* are listed in the GPU's respective SoC file. amdgpu_device.c`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:27` — `extern const struct amdgpu_ip_block_version pp_smu_ip_block;`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:105` — `AMDGPU_PP_SENSOR_GFX_SCLK = 0,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:106` — `AMDGPU_PP_SENSOR_VDDNB,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:107` — `AMDGPU_PP_SENSOR_VDDGFX,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:108` — `AMDGPU_PP_SENSOR_UVD_VCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:109` — `AMDGPU_PP_SENSOR_UVD_DCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:110` — `AMDGPU_PP_SENSOR_VCE_ECCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:111` — `AMDGPU_PP_SENSOR_GPU_LOAD,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:112` — `AMDGPU_PP_SENSOR_MEM_LOAD,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:113` — `AMDGPU_PP_SENSOR_GFX_MCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:114` — `AMDGPU_PP_SENSOR_GPU_TEMP,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:115` — `AMDGPU_PP_SENSOR_EDGE_TEMP = AMDGPU_PP_SENSOR_GPU_TEMP,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:116` — `AMDGPU_PP_SENSOR_HOTSPOT_TEMP,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:117` — `AMDGPU_PP_SENSOR_MEM_TEMP,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:118` — `AMDGPU_PP_SENSOR_VCE_POWER,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:119` — `AMDGPU_PP_SENSOR_UVD_POWER,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:120` — `AMDGPU_PP_SENSOR_GPU_POWER,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:121` — `AMDGPU_PP_SENSOR_STABLE_PSTATE_SCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:122` — `AMDGPU_PP_SENSOR_STABLE_PSTATE_MCLK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:123` — `AMDGPU_PP_SENSOR_ENABLED_SMC_FEATURES_MASK,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:124` — `AMDGPU_PP_SENSOR_MIN_FAN_RPM,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:125` — `AMDGPU_PP_SENSOR_MAX_FAN_RPM,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/kgd_pp_interface.h:126` — `AMDGPU_PP_SENSOR_VCN_POWER_STATE,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:26` — `#include "amdgpu_atombios.h"`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:27` — `#include "amdgpu_i2c.h"`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:28` — `#include "amdgpu_dpm.h"`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:31` — `#include "amdgpu_display.h"`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:36` — `void amdgpu_dpm_print_class_info(u32 class, u32 class2)`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:87` — `void amdgpu_dpm_print_cap_info(u32 caps)`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:99` — `void amdgpu_dpm_print_ps_status(struct amdgpu_device *adev,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:100` — `struct amdgpu_ps *rps)`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:112` — `void amdgpu_dpm_get_active_displays(struct amdgpu_device *adev)`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:116` — `struct amdgpu_crtc *amdgpu_crtc;`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:123` — `amdgpu_crtc = to_amdgpu_crtc(crtc);`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:124` — `if (amdgpu_crtc->enabled) {`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:125` — `adev->pm.dpm.new_active_crtcs |= (1 << amdgpu_crtc->crtc_id);`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/pm/amdgpu_dpm.c:133` — `u32 amdgpu_dpm_get_vblank_time(struct amdgpu_device *adev)`
- [truncated; 13562 additional matches]

### cs_submit

Found 3 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/drivers/gpu/drm/samsung/gpu/sgpu/amdgpu_cs.c:1376` — `static int amdgpu_cs_submit(struct amdgpu_cs_parser *p,`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/sgpu/amdgpu_cs.c:1398` — `* The lock is held until amdgpu_cs_submit is finished and fence is`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/sgpu/amdgpu_cs.c:1515` — `r = amdgpu_cs_submit(&parser, cs);`

### memory_vm

Found 61149 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:709` — `#iommu-cells = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:715` — `#iommu-cells = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:678` — `#iommu-cells = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:670` — `#iommu-cells = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-universal9945-cp-s5153ap-sipc.dtsi:691` — `#iommu-cells = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-audio.dtsi:31` — `iommus = <&sysmmu_aud_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-audio.dtsi:33` — `samsung,iommu-group = <&iommu_group_aud>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:202` — `pablo,iommu-group = <&is_iommu_group0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:247` — `iommus = <&sysmmu_icpu_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:248` — `samsung,iommu-group = <&iommu_group_icpu>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:252` — `samsung,iommu-reserved-map =`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:502` — `iommus =`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:510` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:602` — `iommus = <&sysmmu_lme_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:603` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:606` — `iommu_group_for_cloader = <1>; /* cloader is not support io coherency */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:619` — `iommus = <&sysmmu_lme_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:620` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:621` — `samsung,iommu-identity-map = <0x0 0x1E070000 0x10000>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:624` — `iommu_group_for_cloader = <1>; /* cloader is not support io coherency */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:638` — `iommus = <&sysmmu_csis_s0>,`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:645` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:646` — `samsung,iommu-identity-map =    <0x0 0x1A280000 0x10000>,`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:689` — `is_iommu_group_module: is_iommu_group_module {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:690` — `compatible = "samsung,exynos-is-iommu-group-module";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:691` — `groups = <&is_iommu_group0>, <&is_iommu_group1>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:695` — `iommu-group-module0 = &is_iommu_group_module;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:698` — `is_iommu_group0: is_iommu_group0 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:699` — `compatible = "samsung,exynos-is-iommu-group";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:700` — `iommus = <&sysmmu_mcfp_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:701` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:706` — `is_iommu_group1: is_iommu_group1 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:707` — `compatible = "samsung,exynos-is-iommu-group";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:708` — `iommus = <&sysmmu_lme_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:709` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:713` — `iommu-group0 = &is_iommu_group0;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:714` — `iommu-group1 = &is_iommu_group1;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:945` — `iommus = <&sysmmu_csis_s0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:946` — `samsung,iommu-group = <&iommu_group_is>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:962` — `iommus = <&sysmmu_csis_s0>;`
- [truncated; 61109 additional matches]

### firmware

Found 3247 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-audio.dtsi:69` — `abox_firmware_sram0: abox-firmware-sram0 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-audio.dtsi:76` — `abox_firmware_dram0: abox-firmware-dram0 {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-camera.dtsi:295` — `firmware{`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc.dtsi:29` — `/* Normal MFC0 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc.dtsi:31` — `/* Normal MFC1 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc.dtsi:33` — `/* Secure MFC0 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc.dtsi:35` — `/* Secure MFC1 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc_evt0.dtsi:29` — `/* Normal MFC0 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc_evt0.dtsi:31` — `/* Normal MFC1 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc_evt0.dtsi:33` — `/* Secure MFC0 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945-mfc_evt0.dtsi:35` — `/* Secure MFC1 firmware */`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945.dts:114` — `bootargs = "console=ram printk.devkmsg=on clocksource=arch_sys_counter clk_ignore_unused firmware_class.path=/vendor/firmware rcupdate.rcu_expedited=1 swiotlb=noforce loop.max_part=7 kvm-arm.protected_modules=exynos-hypervisor ttm.pages_lim`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945_evt0-camera.dtsi:281` — `firmware{`
- `kernel/kernel/arch/arm64/boot/dts/exynos/s5e9945_evt0.dts:114` — `bootargs = "console=ram printk.devkmsg=on arm64.nomte clocksource=arch_sys_counter clk_ignore_unused firmware_class.path=/vendor/firmware rcupdate.rcu_expedited=1 allow_mismatched_32bit_el0 swiotlb=noforce loop.max_part=7 kvm-arm.protected_`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:46` — `#define mmSDMA0_UCODE_CHECKSUM_DEFAULT                                           0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:544` — `#define mmSDMA1_UCODE_CHECKSUM_DEFAULT                                           0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:4607` — `#define mmRLC_PERFMON_CLK_CNTL_UCODE_DEFAULT                                     0x00000001`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:4682` — `#define mmRLC_F32_UCODE_VERSION_DEFAULT                                          0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:4709` — `#define mmRLC_UCODE_CNTL_DEFAULT                                                 0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5155` — `#define mmCP_PFP_UCODE_ADDR_DEFAULT                                              0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5156` — `#define mmCP_PFP_UCODE_DATA_DEFAULT                                              0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5160` — `#define mmCP_CE_UCODE_ADDR_DEFAULT                                               0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5161` — `#define mmCP_CE_UCODE_DATA_DEFAULT                                               0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5162` — `#define mmCP_MEC_ME1_UCODE_ADDR_DEFAULT                                          0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5163` — `#define mmCP_MEC_ME1_UCODE_DATA_DEFAULT                                          0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5164` — `#define mmCP_MEC_ME2_UCODE_ADDR_DEFAULT                                          0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5165` — `#define mmCP_MEC_ME2_UCODE_DATA_DEFAULT                                          0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5242` — `#define mmRLC_HYP_RLCG_UCODE_CHKSUM_DEFAULT                                      0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5243` — `#define mmRLC_HYP_RLCP_UCODE_CHKSUM_DEFAULT                                      0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5244` — `#define mmRLC_HYP_RLCV_UCODE_CHKSUM_DEFAULT                                      0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5264` — `#define mmRLC_GPM_UCODE_ADDR_DEFAULT                                             0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5265` — `#define mmRLC_GPM_UCODE_DATA_DEFAULT                                             0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5266` — `#define mmRLC_PACE_UCODE_ADDR_DEFAULT                                            0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5267` — `#define mmRLC_PACE_UCODE_DATA_DEFAULT                                            0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5268` — `#define mmRLC_GPU_IOV_UCODE_ADDR_DEFAULT                                         0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5269` — `#define mmRLC_GPU_IOV_UCODE_DATA_DEFAULT                                         0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5287` — `#define mmSDMA0_UCODE_ADDR_DEFAULT                                               0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5288` — `#define mmSDMA0_UCODE_DATA_DEFAULT                                               0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5303` — `#define mmSDMA1_UCODE_ADDR_DEFAULT                                               0x00000000`
- `kernel/kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/gc_10_1_0_default.h:5304` — `#define mmSDMA1_UCODE_DATA_DEFAULT                                               0x00000000`
- [truncated; 3207 additional matches]

### reset_scheduler

Found 25346 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:218` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:242` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:275` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:306` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:331` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:357` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:383` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:414` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:440` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:218` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:242` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:275` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:306` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:331` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:357` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:383` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:414` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:440` — `samsung,reset-before-trans;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:34` — `interrupts = <GIC_SPI INTREQ_RESET_REQ IRQ_TYPE_LEVEL_HIGH>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:35` — `interrupt-names = "RESET_REQ";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:62` — `mif,int_ap2cp_cp_reset_noti = <7>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:377` — `iod,attrs = <(IO_ATTR_SBD_IPC | IO_ATTR_SIPC5 | IO_ATTR_STATE_RESET_NOTI)>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:533` — `use_sw_reset_reg = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:34` — `interrupts = <GIC_SPI INTREQ_RESET_REQ IRQ_TYPE_LEVEL_HIGH>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:35` — `interrupt-names = "RESET_REQ";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:62` — `mif,int_ap2cp_cp_reset_noti = <7>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:377` — `iod,attrs = <(IO_ATTR_SBD_IPC | IO_ATTR_SIPC5 | IO_ATTR_STATE_RESET_NOTI)>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:547` — `use_sw_reset_reg = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:32` — `"rf-sub-bd-det-reset-gpio";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:35` — `pinctrl-2 = <&rf_sub_bd_det_reset_gpio>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:39` — `interrupts = <GIC_SPI INTREQ_RESET_REQ IRQ_TYPE_LEVEL_HIGH>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:40` — `interrupt-names = "RESET_REQ";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:67` — `mif,int_ap2cp_cp_reset_noti = <7>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:407` — `iod,attrs = <(IO_ATTR_STATE_RESET_NOTI)>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:440` — `iod,attrs = <(IO_ATTR_NO_CHECK_MAXQ | IO_ATTR_STATE_RESET_NOTI)>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:502` — `use_sw_reset_reg = <0>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:746` — `rf_sub_bd_det_reset_gpio: rf-sub-bd-det-reset-gpio {`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:32` — `"rf-sub-bd-det-reset-gpio";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:35` — `pinctrl-2 = <&rf_sub_bd_det_reset_gpio>;`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:39` — `interrupts = <GIC_SPI INTREQ_RESET_REQ IRQ_TYPE_LEVEL_HIGH>;`
- [truncated; 25306 additional matches]

### device_tree

Found 1306 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:28` — `compatible = "samsung,sensor-flash-s2mpb02";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:227` — `compatible = "samsung,exynos-is-cis-hp2";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:251` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:284` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:315` — `compatible = "samsung,exynos-is-cis-imx754";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:340` — `compatible = "samsung,exynos-is-cis-imx754";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:366` — `compatible = "samsung,exynos-is-cis-3lu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:392` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:423` — `compatible = "samsung,exynos-is-cis-imx564";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:449` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:28` — `compatible = "samsung,sensor-flash-s2mpb02";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:227` — `compatible = "samsung,exynos-is-cis-hp2";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:251` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:284` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:315` — `compatible = "samsung,exynos-is-cis-imx754";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:340` — `compatible = "samsung,exynos-is-cis-imx754";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:366` — `compatible = "samsung,exynos-is-cis-3lu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:392` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:423` — `compatible = "samsung,exynos-is-cis-imx564";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:449` — `compatible = "samsung,exynos-is-actuator-ak737x";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:3` — `* Samsung CP interface device tree source`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:15` — `#include <dt-bindings/interrupt-controller/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:16` — `#include <dt-bindings/clock/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:27` — `compatible = "samsung,exynos-cp";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:527` — `compatible = "samsung,exynos-cp-mailbox";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:590` — `compatible = "samsung,exynos-cp-shmem";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:673` — `compatible = "samsung,direct-dm";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:702` — `compatible = "samsung,cpif-sysmmu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:3` — `* Samsung CP interface device tree source`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:15` — `#include <dt-bindings/interrupt-controller/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:16` — `#include <dt-bindings/clock/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:27` — `compatible = "samsung,exynos-cp";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:541` — `compatible = "samsung,exynos-cp-mailbox";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:604` — `compatible = "samsung,exynos-cp-shmem";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:679` — `compatible = "samsung,direct-dm";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:708` — `compatible = "samsung,cpif-sysmmu";`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:3` — `* Samsung CP interface device tree source`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:15` — `#include <dt-bindings/interrupt-controller/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:16` — `#include <dt-bindings/clock/s5e9945.h>`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:27` — `compatible = "samsung,exynos-cp";`
- [truncated; 1266 additional matches]

### license

Found 2305 matching lines in the kernel tree. The full path inventory is in `kernel-path-inventory.csv`.

- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:4` — `* Copyright (c) 2022 Samsung Electronics Co., Ltd`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:6` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945-erd9945-camera.dtsi:7` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:4` — `* Copyright (c) 2022 Samsung Electronics Co., Ltd`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:6` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/camera/s5e9945_evt0-erd9945-camera.dtsi:7` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:5` — `* Copyright (c) 2019-2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:5` — `* Copyright (c) 2019-2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-cp-s5153ap-sipc_evt0.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:5` — `* Copyright (c) 2019-2022 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:5` — `* Copyright (c) 2019-2022 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5153ap-sit_evt0.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5400-sit.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-cp-s5400-sit.dtsi:5` — `* Copyright (c) 2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss-s5400.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss-s5400.dtsi:5` — `* Copyright (c) 2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss-s5400.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss-s5400.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss.dtsi:1` — `// SPDX-License-Identifier: GPL-2.0`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss.dtsi:5` — `* Copyright (c) 2022 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss.dtsi:8` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945-gnss.dtsi:9` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945.dts:4` — `* Copyright (c) 2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945.dts:7` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945.dts:8` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945_common.dtsi:4` — `* Copyright (c) 2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945_common.dtsi:11` — `* This program is free software; you can redistribute it and/or modify`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945_common.dtsi:12` — `* it under the terms of the GNU General Public License version 2 as`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945_r01.dts:4` — `* Copyright (c) 2023 Samsung Electronics Co., Ltd.`
- `kernel/kernel/arch/arm64/boot/dts/exynos/board/erd/s5e9945-erd9945_r01.dts:7` — `* This program is free software; you can redistribute it and/or modify`
- [truncated; 2265 additional matches]

## Platform references

Found 0 matching platform lines for `s5e9945`, `erd9945`, `samsung-sgpu`, `vulkan.samsung`, or `sgpu`.


## Legal and evidentiary boundary

The source archive includes Samsung kernel/platform material and generated or vendor-adjacent components. This repository records paths and observations only. Before copying code, headers, generated register tables, firmware, or binaries into the project, complete a file-level license and provenance review.
