# Supplied Archive Contents

The supplied Quick Share archive is `Tudo sobre a xclipse.zip`. It has SHA-256 `cd73e2fa24ac083b9babf243b39772065b060abc9cd30fb476acabe74433e67f`, 17 members, and 16 files. The archive is kept outside Git; this file records its structure and the derived analysis artifacts.

| Member | Size | Role |
| --- | ---: | --- |
| `Backup_completo_de_continuidade_—_projeto_Xclipse_.md` | 11,816 B | Continuity record and interpretation of previous experiments. |
| `Relatório_técnico_—_caminho_do_ICD_Vulkan_Xclipse_.md` | 4,846 B | Loader/ICD report. |
| `SAVE_retomada_caminho_implementação Vulkan de referência.pdf` | 111,955 B | Continuity save. |
| `SM-S721B.zip` | 414,414,944 B | Device/source package; contains the Samsung source release and regional Device Tree archives. |
| `XCLIPSE940_PROBE_FINAL.zip` | 12,031,897 B | SGPU/Vulkan probes, outputs, and reference binaries. |
| `XCLIPSE940_problemas_etapa3_em_diante.pdf` | 73,129 B | Failure and diagnosis report. |
| `backup_completo_continuidade.pdf` | 118,886 B | PDF form of the continuity report. |
| `caminho_implementação Vulkan de referência.zip` | 158,279 B | Reports, probe source, evidence transcript, metadata, and hashes. |
| `exec_props.txt` | 183 B | Termux Vulkan result: llvmpipe only. |
| `exec_props_root.txt` | 183 B | Root-session Vulkan result: llvmpipe only. |
| `libdrm_sgpu.so` | 134,568 B | Vendor DRM wrapper copied for offline reference. |
| `logs para tentativa implementação Vulkan de referência_xclipse.zip` | 365,738 B | Device, DRM, SGPU, Vulkan, ABI, and architecture logs. |
| `probe_SM-S721B.txt` | 1,274 B | Raw SGPU probe output. |
| `relatorio_tecnico_icd_vulkan.pdf` | 67,841 B | PDF form of the ICD report. |
| `sgpu_raw_probe` | 10,896 B | AArch64 probe binary. |
| `vulkan.samsung.so` | 44,423,944 B | Vendor Vulkan ICD binary copied for offline reference. |

## Nested source release

`SM-S721B.zip` contains `SM-S721B_16_Opensource.zip` (405,898,948 bytes), which contains `Kernel.tar.gz`, `Platform.tar.gz`, `README_Kernel.txt`, and `README_Platform.txt`. The extracted source tree includes an SGPU kernel driver, an SGPU UAPI header, s5e9945 Device Tree files, Samsung IOMMU and DMA-BUF support, Android/platform code, and related build material.

The source archive is not copied into this repository. See `kernel-source-review.md`, `selected-paths.md`, `kernel-path-inventory.csv`, `inventory.csv`, and `license-inventory.md` for derived indexes.

## Interpretation boundary

The existence of a file named `probe`, `test`, `exec`, or `diagnostico` does not establish the underlying capability. See `reports/initial-evidence.md` and `reports/test-classification.md` for the execution-specific review.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
