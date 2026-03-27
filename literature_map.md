# Part 1: Literature Map Overview

## 1) Architectural evolution map

This collection is organized around the transition chain:

1. **CPU-orchestrated stacks** (host-mediated POSIX and kernel/user I/O paths)
2. **GPU-aware integration** (GPU-visible file abstractions, accelerator-aware runtimes)
3. **GPU-direct / bypass data paths** (DMA, GPUDirect RDMA/GDS, reduced host copies)
4. **Fabric and deployment evolution** (NVMe/NVMe-oF/RDMA/disaggregation)
5. **Workload-driven storage design** (AI input pipelines, checkpoint/offload, distributed training)
6. **GPU-driven paradigms** (GPU-initiated and co-designed storage control paths)

## 2) Coverage by required primary categories

- **A. Traditional CPU-Orchestrated I/O and Bottlenecks**: `linux_io_uring`, `spdk_overview`
- **B. GPU-Aware / GPU-Integrated Storage Architectures**: `silberstein2013gpufs`, `shahar2016gpufs`, `geminifs2025`
- **C. GPU Direct / Bypass Data Path Mechanisms**: `nvidia_gds_blog`, `nvidia_gds_overview`, `nvidia_gdrdma`
- **D. NVMe / NVMe-oF / RDMA / Disaggregation**: `nvme_spec`, `nvmeof_spec`, `pcie_spec`, `infiniband_spec`, `liu2017rackscale`
- **E. File Systems / Caching / Data Pipelines for GPU/AI**: `murray2021tfdata`, `hoard2018`
- **F. Checkpointing / Recovery / Persistence for GPU Training**: `rajbhandari2021zeroinfinity`, `qiu2025geminifs`
- **G. Emerging GPU-Driven / Co-Designed Paradigms**: `qureshi2023bam`, `benhaim2025path`
- **H. Existing Surveys / Reviews / Position Papers**: `zhang2025hpcstoragesurvey`, `traub2023compstoragesurvey`

## 3) Shortlist

### Foundational papers / artifacts
- GPUfs (ASPLOS 2013)
- GPUDirect Storage technical introduction (NVIDIA, 2019)
- NVMe Base Specification (NVM Express)
- InfiniBand Architecture Specification

### Representative systems papers
- Supporting data-driven I/O on GPUs using GPUfs (SYSTOR 2016)
- BaM (ASPLOS 2023)
- GeminiFS (FAST 2025)
- GPU Direct I/O with HDF5 (PDSW 2020 workshop)

### Important surveys / position works
- Survey of storage systems in high performance computing (CCF THPC, 2025)
- A Survey of Computational Storage (UChicago TR, 2023)
- Understanding Rack-Scale Disaggregated Storage (HotStorage 2017; position + evaluation)

### Recent trend papers
- GeminiFS (FAST 2025)
- Path to GPU-Initiated I/O for Data-Intensive Systems (DaMoN 2025)
- Data Movement-Aware GPU Sharing for Data-Intensive Systems (CIDR 2026, outside bib core set)

---

# Part 2: Structured Paper Table

| Key | Title | Venue/Type | Year | Primary | Secondary Tags | Summary (2–4 sentences) | Relevance note |
|---|---|---|---:|---|---|---|---|
| `silberstein2013gpufs` | GPUfs: Integrating a file system with GPUs | ASPLOS / conference | 2013 | B | GPU, I/O, file system, storage stack | GPUfs introduces a file-system interface that allows GPU kernels to directly issue file operations, reducing reliance on host-side orchestration for data-intensive GPU applications. It defines a consistency model and runtime support that preserves programmability while exposing parallel access from many GPU threads. This paper is one of the earliest concrete designs for GPU-aware storage abstractions. | Establishes the architectural pivot from pure CPU mediation toward GPU-integrated I/O abstractions. |
| `shahar2016gpufs` | Supporting data-driven I/O on GPUs using GPUfs | SYSTOR / conference | 2016 | B | GPU, I/O, file system, prefetching | This work extends and evaluates GPUfs in data-driven workloads, focusing on making GPU-side I/O practical under realistic access patterns. It highlights constraints such as access granularity and control overheads that emerge when GPUs become active I/O participants. The study gives operational insight beyond the initial design. | Bridges prototype capability and system behavior under production-like data flows. |
| `qureshi2023bam` | GPU-Initiated On-Demand High-Throughput Storage Access in the BaM System Architecture | ASPLOS / conference | 2023 | G | GPU, I/O, storage stack, computational storage | BaM proposes GPU-initiated access and scheduling for storage interactions, reducing CPU control-path involvement in fine-grained data-intensive pipelines. The architecture co-designs runtime and data-path mechanisms to sustain high throughput without host bottlenecks. It demonstrates that GPUs can drive I/O control decisions rather than only consume fetched data. | Core exemplar of GPU-driven storage control-plane evolution. |
| `geminifs2025` | GeminiFS: A Companion File System for GPUs | FAST / conference | 2025 | B (secondary F) | GPU, file system, checkpoint, AI training, HPC | GeminiFS presents a GPU-centered companion filesystem motivated by modern distributed AI/HPC workflows. It focuses on reducing bottlenecks in data ingest and model-state persistence paths when accelerators dominate compute cycles. The system provides concrete design points for integrating filesystem behavior with GPU execution characteristics. | Represents state-of-the-art GPU-integrated filesystem design and training-oriented persistence considerations. |
| `nvidia_gds_blog` | GPUDirect Storage: A Direct Path Between Storage and GPU Memory | NVIDIA blog / report | 2019 | C | GPU, I/O, zero-copy, DMA, NVMe SSD | This foundational industrial report explains GDS motivation and architecture: bypassing host bounce buffers and enabling direct DMA from storage to GPU memory. It frames where latency and CPU overhead arise in traditional stacks and why bypass paths matter. It also seeded broad ecosystem adoption and follow-on implementations. | Key milestone in the move from GPU-aware software to GPU-direct data paths. |
| `nvidia_gds_overview` | NVIDIA Magnum IO GPUDirect Storage Overview | Documentation / report | 2026 | C | GPU, I/O, storage stack, NVMe SSD | The overview details software/hardware prerequisites, data-path behavior, and integration points for GDS deployments. It is a practical reference for understanding real deployment constraints, including compatibility and path selection. It complements academic designs with operational guidance. | Essential for mapping architectural ideas to deployable stacks. |
| `nvidia_gdrdma` | NVIDIA GPUDirect RDMA | Documentation / report | 2022 | C | GPU, RDMA, disaggregation, I/O | This documentation describes direct network-device to GPU-memory transfers and required memory-registration semantics. It clarifies bypass behavior and interaction with NICs/fabrics. For GPU storage over networked paths, GPUDirect RDMA is a core transport enabler. | Connects local GPU-direct concepts to remote/disaggregated storage paths. |
| `nvme_spec` | NVM Express Base Specification Revision 2.0d | Specification / report | 2024 | D | NVMe SSD, storage stack, I/O | NVMe defines queue-based host-controller interaction with high parallelism and low software overhead. The specification underpins local SSD behavior that GPU-integrated stacks must exploit. Understanding queue semantics and command/completion behavior is crucial for GPU-era storage tuning. | Provides the baseline block-interface model beneath GPU-integrated systems. |
| `nvmeof_spec` | NVMe over Fabrics Specification Revision 1.1 | Specification / report | 2021 | D | NVMe-oF, RDMA, disaggregation | NVMe-oF extends NVMe semantics over network fabrics, enabling remote low-latency block access. It is central to disaggregated storage architectures and remote SSD pools used by accelerator clusters. The spec defines transport bindings and protocol behavior relevant to GPU-scale deployments. | Forms the protocol basis for remote GPU-accessible block storage architectures. |
| `pcie_spec` | PCI Express Base Specification Revision 6.0 | Specification / report | 2022 | D | I/O, storage stack, GPU | PCIe governs host-device and device-device bandwidth/latency behavior in most accelerator servers. Revision changes influence practical throughput ceilings and contention patterns across GPUs, NICs, and SSDs. Any end-to-end GPU storage design must account for PCIe topology and limits. | Defines the physical/logical substrate for many GPU-storage data paths. |
| `infiniband_spec` | InfiniBand Architecture Specification Volume 1 | Specification / report | 2023 | D | RDMA, disaggregation, HPC | The IBTA specification defines RDMA-capable transport semantics widely used in HPC and AI clusters. It is a key foundation for low-latency remote data paths and storage disaggregation. The standard informs architectural decisions about queueing, ordering, and transport guarantees. | Core fabric reference for networked GPU I/O systems. |
| `liu2017rackscale` | Understanding Rack-Scale Disaggregated Storage | HotStorage / workshop | 2017 | D | disaggregation, storage stack, HPC | This work explores rack-scale disaggregated storage design points and evaluates trade-offs in resource utilization and performance. It provides a system-level perspective on when disaggregation helps and what bottlenecks emerge. Although not GPU-specific, it is directly relevant to accelerator-era remote storage provisioning. | Supplies deployment-level context for GPU-accessible storage pooling. |
| `murray2021tfdata` | tf.data: A Machine Learning Data Processing Framework | PVLDB / journal track | 2021 | E | data loading, caching, prefetching, AI training, MLSys | tf.data studies and optimizes ML input pipelines, showing that data ingestion can consume a large fraction of training time. It introduces pipeline transformations and runtime strategies (parallelism, caching, optimization) that shape storage pressure seen by accelerators. The paper provides workload evidence and practical mechanisms. | Grounds GPU-storage architecture in real ML data pipeline behavior. |
| `hoard2018` | Hoard: A Distributed Data Caching System to Accelerate Deep Learning Training on the Cloud | arXiv preprint / influential system report | 2018 | E | caching, data loading, AI training, GPU, NVMe SSD | Hoard proposes distributed caching across local disks on GPU nodes to reduce central storage bottlenecks. It demonstrates substantial training speedups by avoiding I/O starvation. While presented as a preprint, it is influential in discussing storage-aware training throughput. | Highlights how cache/distribution strategy impacts GPU utilization. |
| `rajbhandari2021zeroinfinity` | ZeRO-Infinity: Breaking the GPU Memory Wall for Extreme Scale Deep Learning | SC / conference + arXiv | 2021 | F | checkpoint, recovery, AI training, NVMe SSD, GPU | ZeRO-Infinity uses coordinated GPU/CPU/NVMe memory hierarchy management to scale model training beyond device memory limits. It demonstrates that storage-tier participation is central to model-scale growth. The work is pivotal for persistence/offload discussions in LLM-scale training. | Connects storage hierarchy design with GPU training scalability and resilience workflows. |
| `hdf5gds2020` | GPU Direct I/O with HDF5 | PDSW@SC / workshop | 2020 | F (secondary C) | GPU, I/O, file system, HPC, checkpoint | This workshop paper reports integrating GPUDirect Storage into HDF5 via a VFD path to support existing scientific I/O libraries. It shows how GPU-direct mechanisms can be adopted without rewriting full application stacks. The design is important for HPC checkpoint and analysis workflows. | Bridges ecosystem libraries and GPU-direct techniques for real workflows. |
| `benhaim2025path` | Path to GPU-Initiated I/O for Data-Intensive Systems | DaMoN / workshop-position | 2025 | G | GPU, I/O, storage stack, near-data processing | This position/vision paper synthesizes lessons from GPUfs, GDS, and newer GPU-initiated designs, then outlines barriers to practical adoption. It emphasizes software-stack complexity and compatibility constraints. It is useful for framing research agenda items for GPU-driven storage. | Provides forward-looking synthesis for the final transition phase of the survey narrative. |
| `zhang2025hpcstoragesurvey` | Survey of storage systems in high performance computing | CCF THPC / journal survey | 2025 | H | storage stack, HPC, RDMA, NVMe-oF | The survey reviews HPC storage evolution and technologies such as distributed and parallel filesystems plus modern interconnect/storage protocols. It is useful for baseline context and terminology but is not GPU-control-plane-centric. It offers broad background for anchoring GPU-specific contributions. | Good baseline survey to contrast against GPU-integrated focus. |
| `traub2023compstoragesurvey` | A Survey of Computational Storage and Near-Data Processing in SSDs | UChicago Technical Report | 2023 | H | computational storage, near-data processing, storage stack | This survey/report synthesizes computational storage proposals and analyzes when offloading to storage devices is beneficial. It is not specific to GPUs, but its design dimensions map directly to accelerator co-design questions. It is valuable for discussing storage-compute role partitioning. | Supports the co-design thread in GPU-driven and near-data paradigms. |

---

# Part 3: Existing Survey Landscape and Gaps

## What prior surveys/reviews cover well

1. **HPC storage surveys** (e.g., `zhang2025hpcstoragesurvey`) cover parallel/distributed filesystems, protocol trends, and system deployment practices.
2. **Computational storage surveys** (e.g., `traub2023compstoragesurvey`) analyze offload opportunities and constraints in storage devices.
3. **ML systems pipeline papers/surveys** (e.g., `murray2021tfdata`) characterize data ingestion and preprocessing overheads at scale.

## What they do NOT cover sufficiently

1. **Control-plane transition** from CPU-led orchestration to GPU-initiated request generation.
2. **Unified taxonomy** spanning local direct I/O (GDS), network direct paths (GPUDirect RDMA), and disaggregated deployments (NVMe-oF + RDMA).
3. **Cross-layer co-analysis** of standards/spec constraints (PCIe/NVMe/IB/NVMe-oF) with filesystem and AI workload behavior.
4. **Checkpoint/persistence coupling** specific to GPU-scale training and LLM-era offload tiers.

## How the new survey differs

- It treats GPU-integrated storage as an **architectural evolution problem** (control path + data path + deployment + workload) rather than a list of technologies.
- It links **systems papers, standards, and industrial artifacts** into one narrative and taxonomy.
- It explicitly targets the progression: **CPU orchestrated → GPU aware → GPU direct → GPU driven**.

---

# Part 4: Initial `references.bib`

The initial BibTeX set is provided in `references.bib` at repository root and aligns with the keys used above.
