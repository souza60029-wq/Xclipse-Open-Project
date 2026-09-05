# Compute Pipeline

**Status:** no end-to-end compute execution is demonstrated.

The project plan correctly defines the minimum compute experiment as allocation, binding, dispatch, synchronization, and validated readback. The supplied SGPU probe stops before command submission. The supplied Vulkan probe creates a trivial compute pipeline but never retrieves a queue, records a command buffer, calls `vkCmdDispatch`, submits work, waits for completion, or reads a result.

| Required step | Supplied evidence | Status |
| --- | --- | --- |
| Allocate a resource | 64 KiB GEM allocation exists in the DRM probe. | Partial; CPU/GTT path only. |
| Bind/map for GPU use | VA map/unmap succeeds. | Partial; no GPU access proven. |
| Record dispatch | No `vkCmdDispatch` in the Vulkan probe. | Missing. |
| Submit | No queue submit in either supplied probe. | Missing. |
| Synchronize | No execution fence or queue completion. | Missing. |
| Validate readback | No GPU-produced result. | Missing. |

The correct next milestone is a bounded, reversible compute test after the Android loader, queue, synchronization, and reset paths are understood. A line such as `compute pipeline ok` must remain classified as pipeline creation, not compute execution.

## References

[1]: https://registry.khronos.org/vulkan/specs/1.3-extensions/html/ "Vulkan API specification"
[2]: https://quickshare.samsungcloud.com/cN3RdfqvjU6y "Quick Share archive supplied for Xclipse Open Project analysis"
