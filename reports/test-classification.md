# Test and Probe Classification

This report is a conservative filename/content triage of the supplied archive. It is not a test result. A “possible execution test” still requires manual inspection of target-device selection, resource allocation, submission, synchronization, and validated output.

| Archive member | Conservative class | Cold interpretation |
| --- | --- | --- |
| `Tudo sobre a xclipse/Backup_completo_de_continuidade_—_projeto_Xclipse_.md` | continuity report | Narrative and plans; not a runtime result by itself. |
| `Tudo sobre a xclipse/Relatório_técnico_—_caminho_do_ICD_Vulkan_Xclipse_.md` | loader/ICD report | Interpretation of loader observations; not a driver test. |
| `Tudo sobre a xclipse/SAVE_retomada_caminho_implementação Vulkan de referência.pdf` | continuity PDF | Plans, commands, and conclusions requiring artifact cross-check. |
| `Tudo sobre a xclipse/SM-S721B.zip` | source/device package | Contains Samsung source and platform material; not a test. |
| `Tudo sobre a xclipse/XCLIPSE940_PROBE_FINAL.zip` | probe package | Contains initializers, raw outputs, and source; execution status depends on each file. |
| `Tudo sobre a xclipse/XCLIPSE940_problemas_etapa3_em_diante.pdf` | failure report | Documents failures and limits; not proof of successful execution. |
| `Tudo sobre a xclipse/backup_completo_continuidade.pdf` | continuity PDF | Narrative and plans; not a runtime result by itself. |
| `Tudo sobre a xclipse/caminho_implementação Vulkan de referência.zip` | probe/report package | Contains a probe explicitly described as not submitting GPU work. |
| `Tudo sobre a xclipse/exec_props.txt` | negative Vulkan observation | One llvmpipe device; no Samsung device visible. |
| `Tudo sobre a xclipse/exec_props_root.txt` | negative Vulkan observation | Root-session repeat; no Samsung device visible. |
| `Tudo sobre a xclipse/libdrm_sgpu.so` | reference vendor binary | Exported AMDGPU-like symbols; not evidence that the probe executed GPU work. |
| `Tudo sobre a xclipse/logs para tentativa implementação Vulkan de referência_xclipse.zip` | raw device/log package | Environment and diagnostic outputs; classify each command separately. |
| `Tudo sobre a xclipse/probe_SM-S721B.txt` | raw SGPU probe output | Confirms queries and memory/VM smoke path; no submission. |
| `Tudo sobre a xclipse/relatorio_tecnico_icd_vulkan.pdf` | loader/ICD report | Interpretation of observations; not an execution test. |
| `Tudo sobre a xclipse/sgpu_raw_probe` | AArch64 probe binary | Its source explicitly submits nothing; classify as bring-up probe. |
| `Tudo sobre a xclipse/vulkan.samsung.so` | reference vendor binary | Present on device/archive; not evidence of loader usability or execution. |

## Promotion rule

No row is promoted to a real execution test from a name, a successful build, a test framework assertion, or an API return code alone. Promotion requires an evidence report with raw output and an end-to-end result.
