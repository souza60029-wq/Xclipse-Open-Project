# Ramificação técnica da Xclipse 940

Esta é a **ramificação técnica** do hardware, não a árvore de arquivos do projeto. Ela representa os caminhos observados no Device Tree, kernel, GPU, memória, energia, IOMMU e Android.

```text
SoC Exynos / plataforma SM-S721B
└── G3D / Xclipse 940
    ├── /sys/firmware/devicetree/base/sgpu@22200000
    │   ├── compatible: samsung-sgpu,samsung-sgpu
    │   ├── reg-names: gpu, doorbell, debug, pwrctl, sysreg, htu
    │   ├── gpu_pm
    │   ├── gpu_doorbell
    │   ├── gpu_debug
    │   ├── gpu_smntarg
    │   └── gpu_sysreg
    ├── IRQ
    │   ├── SGPU
    │   └── GPU-AFM
    ├── Energia e clocks
    │   ├── power-domains -> pd_g3dcore@0 / phandle 0xc3
    │   ├── freq_table / min_freq / max_freq
    │   ├── IFPO
    │   ├── devfreq G3D
    │   └── thermal-zones/G3D
    ├── Memória
    │   ├── reserved-memory/sgpu_rmem
    │   ├── reserved-memory/gpu_buffer
    │   └── gpu_buffer_dma_heap
    ├── IOMMU / SysMMU
    │   ├── grupos Samsung sysmmu-group-v9
    │   ├── streams relacionados a G3D/MMU
    │   └── vínculos de DMA e IOVA
    ├── Interconnect / QoS
    │   ├── exynos-bts/bts_g3dmmu
    │   ├── bts_g3d0
    │   ├── bts_g3d1
    │   ├── bts_g3d2
    │   └── bts_g3d3
    ├── Kernel DRM/SGPU
    │   ├── BO / GEM / DMA-BUF
    │   ├── VM / PTE / PDE / flush
    │   ├── scheduler
    │   ├── ring gfx_0.0.0
    │   └── IB submission
    └── Android vendor stack
        ├── libdrm_sgpu.so
        ├── vulkan.samsung.so
        ├── libOpenCL.so
        ├── libSGPUOpenCL.so
        ├── SurfaceFlinger / RenderThread
        └── namespaces e SELinux
```

## Caminho operacional observado

```text
RenderThread / Gralloc
  -> sgpu_pio_map_queue
  -> amdgpu_vm_bo_cs
  -> amdgpu_vm_flush
  -> amdgpu_cs_ioctl
  -> amdgpu_sched_run_job
  -> amdgpu_ib_schedule
  -> ring gfx_0.0.0
  -> eventos de limpeza/unmap
```

O mapa mostra relações e pontos de observação. Não transforma nomes de nós em uma especificação completa do hardware. Endereços, flags e formatos devem ser validados por coletas controladas e pela fonte de kernel licenciada.

## Próximo refinamento

O próximo mapa deve adicionar, para cada recurso, a origem do phandle, a unidade de clock/reset, a relação IOMMU/stream e o mecanismo de erro. O objetivo é produzir uma descrição reproduzível antes de escrever um cliente de GPU.
