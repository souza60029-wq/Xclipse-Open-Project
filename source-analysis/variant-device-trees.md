# Variant Device Tree evidence from `SM-S721B.zip`

The newly supplied outer bundle was compared against the Samsung source archive already used by this project. Its nested `SM-S721B_16_Opensource.zip` has the exact same SHA-256 as the previously analyzed source archive, so the main kernel, SGPU driver, UAPI, Device Tree platform files and firmware references remain the same evidence set.

The outer bundle adds four small variant packages:

| Variant package | Board families observed | Immediate value |
| --- | --- | --- |
| `SM-S7210_CHN_16_Opensource_dts.zip` | `r12s_chn_hkx` revisions `r00`, `r01`, `r02`, `r04`, `r08`, `r09` | Board/revision comparison for the China variant |
| `SM-S721J_JPN_16_Opensource_dts.zip` | `r12s_jpn_kdix` revisions `r00`, `r01`, `r02`, `r04`, `r08`, `r09` | Board/revision comparison for the Japan variant |
| `SM-S721Q_JPN_16_Opensource_dts.zip` | `r12s_jpn_openx` revisions `r00`, `r01`, `r02`, `r04`, `r08`, `r09` | Board/revision comparison for the open Japan variant |
| `SM-S721U_S721U1_S721W_NA_16_Opensource_dts.zip` | `r12s_usa_singlew` revisions `r00`, `r01`, `r02`, `r04`, `r08`, `r09` | Board/revision comparison for North America variants |

These packages primarily contain board-level DTS files and selected includes. They do not replace the main `s5e9945-sgpu_common.dtsi` and `s5e9945-sgpu_evt0.dtsi` source paths already analyzed. Their first value is comparative: they can reveal which board and revision selects which common platform includes, clock bindings, power configuration and build-specific overlays.

## Correct conclusion

This new attachment does not invalidate or replace the previous source analysis. It **confirms the same main source archive** and expands the evidence with cross-variant Device Tree material. The next useful comparison is not to treat every board DTS difference as a GPU difference, but to separate:

1. GPU/platform-common properties;
2. board-specific power, display and peripheral properties;
3. revision-specific overlays;
4. build-specific changes unrelated to SGPU.

The extracted variant sources remain outside Git. Only hashes, inventories and derived path analysis are published.

## Reproducibility

The outer archive and nested hashes are recorded in `data/sm-s721b-bundle-manifest.txt`. The raw archive is not redistributed by this repository.
