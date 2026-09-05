# Validation and Evidence Protocol

## Principle

Every discovery must be independently understandable. A report must say what was tested, on which device and revision, with which build and firmware, using which command or binary, what raw output was produced, what interpretation is justified, and which uncertainty remains.

## Required experiment record

| Field | Requirement |
| --- | --- |
| Experiment ID | Stable identifier such as `EXP-ANDROID-LOADER-0001`. |
| Date and time | Include timezone and device clock caveats. |
| Operator and host | Record host OS, toolchain, and connection method. |
| Device identity | Model, build fingerprint, SoC, GPU revision, kernel, root state, and SELinux mode. |
| Inputs | Exact binaries, source commit, environment variables, manifests, and configuration. |
| Procedure | Copy-pastable commands, in execution order. |
| Raw output | Preserve as collected; do not beautify or redact silently. |
| Result | State pass, fail, blocked, or inconclusive for the exact question. |
| Interpretation | One claim per sentence, with evidence label. |
| Safety and recovery | Note writes, resets, thermal effects, and recovery steps. |
| Follow-up | Name the smallest next experiment that reduces uncertainty. |

## Test taxonomy

A repository path called `tests/` does not automatically contain tests of GPU behavior. Each test report must classify its content as one or more of the following:

| Class | Definition | Minimum evidence |
| --- | --- | --- |
| Initializer | Prepares directories, loaders, handles, fixtures, or environment. | Initialization completed; no GPU capability claim. |
| Probe | Reads a property, enumerates an object, or observes a state. | Raw output and environment. |
| API smoke test | Calls an API path and checks return codes. | Exact call path, return values, and limitation statement. |
| Execution test | Submits work that must execute on the target GPU. | Submission, synchronization, result validation, and target-device proof. |
| Regression test | Repeats a known execution test against a prior expected result. | Versioned fixture and reproducible comparison. |
| Negative test | Verifies a documented failure or safety boundary. | Expected failure, actual failure, and scope of the negative claim. |

## Never promote these signals alone

The following are insufficient to claim that a real compute or rendering test passed: a test binary compiling; a test directory existing; a fixture being initialized; `vkEnumerateInstanceExtensionProperties` succeeding; a device name string; a reported extension; a command buffer being recorded; a queue handle being created; a call returning `VK_SUCCESS` before execution; or a vendor ICD file existing on disk.

For a real execution claim, the report should prove that the intended target device was selected, a resource was allocated and made visible to the GPU, work was submitted to the intended queue, completion was synchronized, and an independent readback or presentation result matched the expected value. When any step is missing, the claim must be downgraded.

## Evidence levels

**Confirmed** means directly observed and reproduced, or supported by an unambiguous public source. **Confirmed by source** means a path, symbol, interface, or behavior is present in the source archive or authoritative documentation. **Probable** means multiple observations support the claim but a direct test is missing. **Hypothesis** means the claim is only a working possibility. **Discarded** means one approach failed; the label must name the approach and must not be generalized to every alternative.

## Reproducibility and privacy

Raw logs may contain serial numbers, addresses, file paths, memory contents, or security-sensitive details. Preserve raw data locally with hashes. Commit only sanitized fixtures after review. Do not treat redaction as an edit to the raw artifact; keep a separate sanitized derivative with its own hash and provenance.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"
