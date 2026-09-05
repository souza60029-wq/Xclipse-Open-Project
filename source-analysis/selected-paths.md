# Selected Samsung Source Paths

The source archive was extracted locally for analysis and is not committed here. These paths are recorded as evidence pointers. A path's presence does not prove that it is built into the tested image or that its behavior is exposed to user space.

| Concern | Relevant path in source release | Evidence value |
| --- | --- | --- |
| SGPU UAPI | `kernel/include/uapi/drm/sgpu_drm.h` | Defines AMDGPU-derived and Samsung-specific ioctl structures and constants. |
| SGPU driver | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/` | Contains device, KMS, GEM, CS, VM, firmware, GFX, MMHUB, SDMA, scheduler, and Samsung integration code. |
| SGPU build | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/Makefile` | Shows compiled subsystems and optional Samsung features. |
| SGPU configuration | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/Kconfig` | Shows DRM/MMU/firmware/scheduler/TTM dependencies and debug/dump/DVFS options. |
| Platform match | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/amdgpu_drv.c` | Matches `samsung-sgpu,samsung-sgpu`, reads `chip_revision`, and assigns `0x73A0` for Vangogh Lite. |
| s5e9945 common DT | `kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi` | Declares SGPU registers, interrupts, power domain, DMA coherence, and Vangogh Lite flag. |
| s5e9945 EVT0 DT | `kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_evt0.dtsi` | Provides an event-specific chip revision and DVFS/IFPO values. |
| Register headers | `kernel/drivers/gpu/drm/samsung/gpu/include/asic_reg/gc/` | Contains GC 10.4.0 M1/M2 offsets/defaults/sh-mask headers; correlation to a device revision remains required. |
| IOMMU | `kernel/drivers/iommu/samsung/` | Contains Samsung IOMMU support that must be related to the SGPU mapping path. |
| DMA-BUF | `kernel/drivers/dma-buf/heaps/samsung/` | Contains Samsung heap and secure-buffer support relevant to Android memory ownership. |
| Firmware handling | `kernel/drivers/gpu/drm/samsung/gpu/sgpu/amdgpu_ucode.c`, `amdgpu_atomfirmware.c`, `unified_firmware_sign_*.h` | Shows firmware loading/signature material in the source release; not a license to redistribute runtime blobs. |

## Licensing signals

The UAPI header carries an AMDGPU-derived permission notice. The SGPU Kconfig declares `SPDX-License-Identifier: MIT`. The s5e9945 Device Tree file carries a Samsung copyright notice and GPL v2 text. These are file-level signals, not a complete license decision for the entire release. Vendor `.so`, firmware, generated register data, and other files require separate review.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
