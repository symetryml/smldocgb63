# Installation Guide - GPU

Copyright © 2026 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved

## Introduction

SymetryML supports GPU and Multi-GPU projects for hardware-accelerated machine learning on wide datasets. The SymetryML Docker image includes all required CUDA libraries and native libraries pre-installed — no manual GPU software setup is needed inside the container.

To use GPU acceleration, the **host machine** must have NVIDIA drivers and the NVIDIA Container Toolkit installed.

## Prerequisites

* A working SymetryML Docker installation. Refer to the [Installation Guide](./) for setup instructions.
* A SymetryML license that allows GPU or Multi-GPU projects.
* NVIDIA GPU with Compute Capability >= 3.5 on the host.
* NVIDIA drivers installed on the host.
* NVIDIA Container Toolkit installed on the host.

## Host Setup

### Step 1 — Verify NVIDIA Drivers

Run `nvidia-smi` on the host to confirm that NVIDIA drivers are installed and your GPU is detected:

```
$ nvidia-smi
Fri May 26 11:32:04 2023
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 510.47.03    Driver Version: 510.47.03    CUDA Version: 11.6     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla V100-SXM2...  On   | 00000000:00:1E.0 Off |                    0 |
| N/A   41C    P0    27W / 300W |      0MiB / 16384MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

If `nvidia-smi` is not found or does not show your GPU, install the appropriate NVIDIA drivers for your host OS before continuing.

### Step 2 — Install NVIDIA Container Toolkit

The NVIDIA Container Toolkit allows Docker to access the host's GPUs. Install it following the [official NVIDIA Container Toolkit installation guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).

After installation, restart Docker:

```bash
sudo systemctl restart docker
```

## Running SymetryML with GPU

To run SymetryML with GPU support, use the `runtime: nvidia` option and GPU environment variables in your `docker-compose.yml`. Refer to the [GPU Support section of the Installation Guide](./#gpu-support) for the full Docker Compose example.

The key settings are:

| Setting                    | Value                                              | Description                           |
| -------------------------- | -------------------------------------------------- | ------------------------------------- |
| `runtime`                  | `nvidia`                                           | Enables GPU access in the container   |
| `NVIDIA_VISIBLE_DEVICES`   | `all`                                              | Exposes all host GPUs to the container |
| `LD_LIBRARY_PATH`          | `/usr/local/cuda/lib64:/opt/symetry/nativelib`     | CUDA and native library paths          |

## SymetryML Memory Requirements

Please consult the [Technical Requirements](../technical-requirements.md) for more information on memory requirements for various project sizes. Note that with SymetryML, project size is determined by the number of attributes, not the number of rows.

## SymetryML Configuration and GPU

The `symetry-rest.txt` configuration file contains properties that control SymetryML GPU behavior. The following table lists the properties relevant to using SymetryML with NVIDIA GPUs.

| Property                               | Description                                                                                                                                                                                                    |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rtlm.option.rtlm.gpu.matrix.minsize` | Minimum matrix size to use GPU. Matrix operations like multiplication, inversion, etc are used when SymetryML builds models. Other operation like PCA and SVD also can leverage GPU.  Recommended values : 512 |
| `rtlm.option.rtlm.gpu.update.minsize` | Minimum size to use GPU when updating a SymetryML project.  Recommended values:64 to 128                                                                                                                       |
| `rtlm.mgpu.num.gpus`                  | The maximum number of GPUs that can be used on a server in a MultiGPU project.                                                                                                                                 |
| `rtlm.mgpu.runon.one`                 | Enabling that specifies that a MultiGPU project can run on a server with only 1 GPU. '1' enables and '0' disables. Default: '0'                                                                                |
