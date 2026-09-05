# Compute Pipeline

**Status:** a real Samsung compute path is identified; end-to-end project-controlled dispatch and readback are not demonstrated.

The new Etapa 4 evidence corrected the process name and found the supported Photo Remaster path. `com.samsung.android.photoremasterservice:photoremasterservice` loaded `vulkan.samsung.so`, `libdrm_sgpu.so`, `libOpenCL.so`, and `libSGPUOpenCL.so`, with FDs for `/dev/dri/renderD128` and `/dev/dri/card0`. The OpenCL library exposes context and queue creation, buffers/images, program and kernel creation, `clEnqueueNDRangeKernel`, `clEnqueueTask`, `clFlush`, `clFinish`, events, barriers, and `clEnqueueReadBuffer`.

During an observed Photo Remaster operation, the SGPU runtime was reported active, frequency observations changed from 252000 to 500000, and the service continued to hold SGPU DRM FDs. This establishes a strong correlation between a real vendor workload and SGPU activity. It does not identify the exact kernel, prove a successful submit, or prove a validated buffer transition.

| Required step | New evidence | Status |
| --- | --- | --- |
| Reach the production stack | Photo Remaster service loads Vulkan, libdrm, and SGPU OpenCL. | Confirmed path |
| Allocate a resource | Previous 64 KiB GEM/BO probe exists. | Partial; CPU/GTT path only |
| Record a dispatch | OpenCL symbols include `clEnqueueNDRangeKernel`; no project-controlled call trace exists. | Capability present, execution open |
| Submit | `sgpu_cs_submit` and OpenCL execution APIs are present; no successful project trace. | Open |
| Synchronize | OpenCL finish/events/barriers and SGPU sync APIs are available. | Interface mapped, event proof open |
| Validate readback | `clEnqueueReadBuffer` exists; no known input/output pair was captured. | Open |

The minimum compute claim remains:

`BO before → real submit → GPU execution → fence/event → BO after/readback → recovery`

A line such as `compute pipeline ok`, an exported symbol, active frequency, or a vendor workload correlation is not sufficient. The next safe milestone is to observe a controlled Photo Remaster operation or another supported vendor workload with known input and output, before attempting any direct submission experiment.

## References

[1]: ../reports/new-results-analysis.md "Analysis of the new results package"
[2]: ../reports/5-dois-meios-essenciais.md "Five essential discoveries"
[3]: https://registry.khronos.org/OpenCL/specs/3.0-unified/html/OpenCL_API.html "OpenCL API specification"
