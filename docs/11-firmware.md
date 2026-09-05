# Firmware

**Status:** version metadata confirmed; load order, contents, and redistribution status remain open.

The device exposes SGPU firmware metadata through sysfs and the SGPU information query. The supplied capture reports SGPU firmware `2.23.0` and RTL change-list number `0x0004ea15`. Component queries returned ME `0x00000005`, MEC `0x00000004`, PFP `0x00000007`, and RLC `0x00000001`. CE, MC, SDMA, SDMA2, SMC, and the RLC restore-list components reported zero in this capture.

| Component | Observed value | Interpretation |
| --- | ---: | --- |
| SGPU | `2.23.0` | Vendor-facing SGPU version string. |
| SGPU RTL CL | `0x0004ea15` | Runtime metadata associated with this build. |
| ME | `0x5` | Firmware query result. |
| MEC | `0x4` | Firmware query result. |
| PFP | `0x7` | Firmware query result. |
| RLC | `0x1` | Firmware query result. |
| CE/MC/SDMA/SMC | `0x0` | Zero returned; meaning must be verified against driver semantics. |

The source release contains `amdgpu_ucode.c`, `amdgpu_atomfirmware.c`, and signed/unified firmware header material for multiple revisions. The Kconfig can enable built-in firmware, while the default shown for that option is disabled. These source paths document the integration mechanism; they do not grant permission to redistribute runtime blobs or claim that every source variant corresponds to the tested handset.

## Open questions

The project still needs the exact firmware file names loaded on the SM-S721B, load order, signature checks, firmware-to-revision mapping, command-processor responsibilities, and reset behavior. Keep firmware binaries outside Git until provenance and license review are complete.

## References

[1]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
[2]: https://docs.kernel.org/driver-api/firmware/intro.html "Linux firmware loading API"
