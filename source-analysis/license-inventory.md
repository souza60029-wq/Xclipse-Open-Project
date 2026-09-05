# License Inventory

This is an initial automated signal inventory. It is not a legal conclusion. Every high-value source file must be reviewed with its complete notice and provenance before reuse or redistribution.

- Archive SHA-256: `cd73e2fa24ac083b9babf243b39772065b060abc9cd30fb476acabe74433e67f`
- Automated member-level signals: 2
- Source-level signals manually added after extraction: SGPU UAPI, SGPU Kconfig/Makefile, and s5e9945 Device Tree paths

## Files with automated archive-level signals

- `Tudo sobre a xclipse/Backup_completo_de_continuidade_—_projeto_Xclipse_.md`
- `Tudo sobre a xclipse/Relatório_técnico_—_caminho_do_ICD_Vulkan_Xclipse_.md`

## Decision rule

Until this inventory is reviewed, treat Samsung source, firmware, generated binaries, and vendor headers as reference-only. Do not copy implementation into project code.

## Source-level signals

| Path | Signal observed | Decision status |
| --- | --- | --- |
| `kernel/include/uapi/drm/sgpu_drm.h` | AMDGPU-derived permission notice; MIT-style terms in header. | Review exact history and compatibility before reuse. |
| `kernel/drivers/gpu/drm/samsung/gpu/sgpu/Kconfig` | `SPDX-License-Identifier: MIT`. | Confirm scope and file provenance. |
| `kernel/drivers/gpu/drm/samsung/gpu/sgpu/Makefile` | AMD copyright and permission notice. | Preserve notices if reuse is permitted. |
| `kernel/arch/arm64/boot/dts/exynos/s5e9945-sgpu_common.dtsi` | Samsung copyright and GPL v2 text. | Review source release terms and dependencies. |
| `vulkan.samsung.so`, `libdrm_sgpu.so`, firmware | Vendor binaries or runtime material. | Reference-only until explicit redistribution permission is established. |

This inventory is an engineering provenance record, not legal advice. A future contribution must include the complete notice and the reason its license permits inclusion.
