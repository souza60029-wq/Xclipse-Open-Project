# Initial Source Archive Analysis

**Archive:** `Tudo sobre a xclipse.zip`  
**SHA-256:** `cd73e2fa24ac083b9babf243b39772065b060abc9cd30fb476acabe74433e67f`  
**Archive members:** 17 total, 16 files  
**Local extraction:** `/home/ubuntu/xclipse-evidence/source-archive`  

## Scope of this pass

This pass verifies archive integrity, records provenance, classifies members by extension and filename, extracts text-search signals, and creates a license-review queue. It does not assert that a filename is a working test, that source code is redistributable, or that a vendor component can be loaded outside Android.

## Inventory summary

| Class | Count |
| --- | ---: |
| binary | 2 |
| document | 4 |
| log-or-text | 4 |
| other | 4 |
| source-or-config | 2 |

### Extensions

| Extension | Count |
| --- | ---: |
| `.pdf` | 4 |
| `.zip` | 4 |
| `.txt` | 3 |
| `.md` | 2 |
| `.so` | 2 |
| `[none]` | 1 |

## Interpretation boundary

The inventory can establish that files exist in the supplied archive and that the ZIP is readable. It cannot establish that a test executes GPU work. Any directory or executable whose name contains `test`, `probe`, `init`, or `check` must be inspected for the execution path, target-device selection, submission, synchronization, and result validation before being described as a real test.

The next step is a manual and source-aware review of the high-value paths listed in `source-analysis/keyword-index.md`, followed by per-experiment reports with raw outputs.
