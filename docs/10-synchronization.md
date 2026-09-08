# Synchronization

**Status:** planned investigation; no result is claimed until an evidence record is linked.

## Purpose

Document fences, sync objects, semaphores, cache visibility, timeline semantics, and completion evidence.

## Current boundary

The project plan identifies this area as necessary for an independent Xclipse driver, but the supplied plan is not itself proof that the area has been implemented or experimentally validated. This chapter must distinguish source-backed facts, direct observations, probable interpretations, hypotheses, and discarded approaches.

## Evidence table

| Question | Evidence required | Current state | Confidence |
| --- | --- | --- | --- |
| What is known? | Raw artifact, source path, or reproducible output. | Initial plan only. | Hypothesis until an artifact is attached. |
| What is executable? | A test that reaches the target layer and validates a result. | Not demonstrated by the plan. | Not established. |
| What can be reused? | License and provenance review. | Pending archive inventory. | Not established. |

## Method

Start with read-only observations and source inventory. Preserve raw outputs without cosmetic edits. Add an interpretation report beside each raw artifact. If a harness initializes a test but does not submit and validate work on the target GPU, record it as an initializer or probe rather than an execution test.

## Open questions

The chapter remains open until the relevant source paths, device revision, firmware context, safety boundary, and reproducible validation procedure are documented.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://source.android.com/docs/core/architecture/vndk/linker-namespace "Android linker namespaces"

## Addendum 2026-09-07 — xclipselogs

A nova rodada `xclipselogs.zip` acrescentou evidência de um cliente DRM próprio em `/dev/dri/renderD128`. O cliente confirmou abertura do render node, criação de GEM/BO, VA map/unmap, criação de BO_LIST e criação de contexto. Em `A1.5_REAL_CS_20260907_161914`, um `DRM_IOCTL_AMDGPU_CS` foi aceito (`ioctl_ret=0`, `CS_IOCTL=ACCEPTED`) para um chunk IB (`chunk_id=0x1`) com VA `0x4000000000` e `ib_bytes=4`.

Esse resultado confirma **aceitação de uma entrada de command submission pelo KMD**, mas não confirma execução GPU, fence própria, GPU write ou readback. Variantes próximas foram rejeitadas com `EINVAL` ou `EFAULT`/`Bad address`. Este resultado está incorporado à documentação técnica consolidada. Fontes C, executáveis, logs crus e `dmesg` permanecem fora do repositório.
