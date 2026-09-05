# Kernel DRM and UAPI

**Status:** SGPU node, UAPI, platform path, firmware, vendor libdrm and submission interfaces are mapped; a custom submission remains unproven.

The observed DRM layout contains SGPU `card0`/`renderD128` and display `card1`/`renderD129`. The SGPU path is `/sys/devices/platform/22200000.sgpu`, bound to `/sys/bus/platform/drivers/sgpu`, with compatible `samsung-sgpu,samsung-sgpu` at `/sgpu@22200000`.

The source release contains `kernel/include/uapi/drm/sgpu_drm.h` and the SGPU implementation tree. The new package additionally inventories `/vendor/lib64/libdrm_sgpu.so` and symbols for BO allocation/import/export, BO lists, VA operations, context creation, submission, raw submission, reset queries, page-fault queries, fences, semaphores, and sync objects.

| Layer | Confirmed | Not yet confirmed |
| --- | --- | --- |
| Node discovery | SGPU render node, platform binding, `card0`, `renderD128`. | Full ABI behavior across builds. |
| Device identity | SGPU firmware 2.23.0, RTL `0x0004ea15`, devfreq and platform identity. | Meaning of every revision-specific field. |
| Memory | Previous 64 KiB GTT BO, CPU map/touch, VA map/unmap. | GPU access, residency, cache protocol, page-fault recovery. |
| Vendor libdrm | `libdrm_sgpu.so` and relevant exported/string-visible interfaces are present. | A trace proving which interfaces were called and succeeded. |
| Command submission | UAPI and context/submit/sync interfaces are documented. | Safe custom context, IB, submit, fence, execution, and readback. |

An API name such as `sgpu_cs_submit` is evidence of an available interface, not of a successful submission. The next experiment must correlate a real process, its DRM FD, BO/VA state, synchronization object, and validated output.

## References

[1]: ../reports/new-results-analysis.md "Analysis of the new results package"
[2]: ../reports/5-dois-meios-essenciais.md "Five essential discoveries"
[3]: https://docs.kernel.org/gpu/drm-uapi.html "Linux DRM userspace API documentation"
