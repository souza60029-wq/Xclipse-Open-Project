# Shader ISA

**Status:** strong static evidence of shader/compiler/encoding infrastructure; no native Xclipse 940 ISA stream has been captured.

The new Etapa 5 analysis inspected the stripped AArch64 `vulkan.samsung.so` and found strings and code references associated with SPIR-V, shader compilation, opcode translation, hardware opcode selection, instruction emission, and encoding. Relevant names include `SCEmitterGFX103.cpp`, `SCAsmEncoder.cpp`, `SCAsmEncoder.hpp`, `GetOpcode`, `gen_opcode`, `XlateOpcode`, `GetHwOpcode`, `EncodeDPP`, `EncodeSDWA`, `EncodeWaitDepctr`, `EncodeImmediateBuffer`, `EncodeMaccDelay`, `SCEmitVOp1`, `SCEmitScratch`, `SCEmitFlat`, VOPC/VOP1/VOP2/VOP3/VOP3P, DPP8, and DPP16.

The ELF is AArch64, stripped, and carries BuildID `7b6134ba45f561f28b006e07ef075b4d4c429bcd`. The `.text` virtual address was correctly distinguished from the file offset: the disassembly began at `0x143fb30`. This produced real AArch64 instructions, but the first inspected region was not proven to be shader compilation code.

| Evidence | Defensible interpretation |
| --- | --- |
| SPIR-V, shader, compiler, pipeline strings | The vendor binary contains shader-related components or diagnostics. |
| `GetOpcode`, `XlateOpcode`, `GetHwOpcode`, `gen_opcode` | Strong static evidence of opcode-handling concepts; stripped strings are not symbols by themselves. |
| `SCAsmEncoder*`, `Encode*`, VOP/DPP families | Strong evidence of an internal assembly/encoding vocabulary. |
| References ending at `__android_log_print` | Some occurrences are logging/debug paths, not the implementation named by the string. |
| `gfx10_4_GEN` and `MGFX*_GEN` | Do not prove that Xclipse 940 is GFX10.4 or that a specific backend is used at runtime. |
| Real AArch64 disassembly | Confirms native code inspection, not the ISA of the generated shader. |

The current classification is therefore **static compiler/encoding infrastructure strongly supported**, not “Xclipse ISA decoded.” The package still lacks the chain `controlled SPIR-V → Samsung-generated/consumed binary → runtime execution → native instruction correlation`.

## Required next evidence

Obtain a controlled shader through the production Samsung path, capture binary or executable metadata, and correlate at least one instruction or encoding field with a known operation and a runtime result. Keep textual diagnostics, compiler labels, and log strings separate from actual ISA bytes.

## References

[1]: ../reports/new-results-analysis.md "Analysis of the new results package"
[2]: ../reports/5-dois-meios-essenciais.md "Five essential discoveries"
[3]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
