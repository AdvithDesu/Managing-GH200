# GH200 HPC System Overview

## System Architecture

QSIM is a high-performance computing cluster at IIT Madras consisting of 3 nodes:
- **1 Master Node** - For job submission, file management, and administrative tasks (`qsim`)
- **2 GPU Compute Nodes** - For running computational workloads (`qcommthub1` and `qcommthub2`)

All nodes run Ubuntu 22.04 LTS and are connected via high-speed Infiniband NDR 400Gbps network.

## Hardware Specifications

### Master Node (10.24.6.249)

| Component | Specification |
|-----------|---------------|
| **Processor** | 2x Intel Xeon Gold 6542Y |
| **RAM** | 256 GB |
| **Storage** | 2x 960GB SSD + 6x 8TB HDD |
| **OS** | Ubuntu 22.04.3 LTS |
| **Kernel** | 6.8.0-60-generic |

**Purpose**: 
- SSH login point
- Job submission via SLURM
- File editing and script writing
- Package installation
- File transfers

### GPU Compute Nodes (`qcommthub1`, `qcommthub2`)

| Component | Specification |
|-----------|---------------|
| **Processor** | NVIDIA Grace CPU (GH200 Grace Hopper™ Superchip) |
| **RAM** | 480 GB |
| **GPU** | H100 Tensor Core GPU (GH200 Grace Hopper™ Superchip, Air-cooled) |
| **OS** | Ubuntu 22.04.5 LTS arm64 |
| **Kernel** | 6.8.0-1030-nvidia-64k |

**Purpose**:
- Execute compute-intensive jobs
- GPU-accelerated workloads
- CUDA-based applications

## Storage

- **Total storage**: 29 TB available in `/home`
- Shared across all nodes via network filesystem

## Network Infrastructure

### Interconnect
- **Technology**: Mellanox Infiniband NDR
- **Speed**: 400 Gbps
- **Purpose**: High-bandwidth, low-latency communication between nodes

### Network Switch
- **Model**: Tyrone NXT-G48C210R
- 48x 1G (RJ45)
- 4x 25G (SFP28)
- 2x 100G (QSFP28)

## Software Stack

### Resource Management
- **Scheduler**: SLURM 23.11.11

### Compilers
- **GNU Compiler Collection**: gcc, g++, gfortran

### MPI
- **OpenMPI**: For parallel computing and message passing

### High-Speed Interconnect Drivers
- **Mellanox OFED**: MLNX_OFED_LINUX-28.43.2566
