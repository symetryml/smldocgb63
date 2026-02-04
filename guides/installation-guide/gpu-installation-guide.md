# Installation Guide - GPU

Copyright © 2021 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved April 15th, 2021

## Introduction

### Assumptions

* You have a working installation of SymetryML with Jetty. For information about performing this task, refer to the [Installation Guide](./).
* Your SymetryML license allows you to use GPU or MultiGPU SymetryML projects.

### GPU Support Requirements

| Requirement      | Description                                                     |
| ---------------- | --------------------------------------------------------------- |
| CUDA Library     | The SymetryML software is currently certified CUDA Version 10.x |
| Operating System | CentOS 7.x or Amazon Linux based on RedHat 7.x.                 |
| Supported GPUs   | NVIDIA GPU with Compute Capability >= 3.5                       |

## Configuration Information

SymetryML is a Java software that relies on certain native library (.so files) to work in tandem with NVIDIA GPU.

### Using SymetryML Native Library

As mentioned in the [Installation Guide](./) once you decompress the `symetry.tar.gz` archive into `/opt/symetry`, you will install the necessary libraries to use NVIDIA gpus into the `/opt/symetry/nativelib` folder. Your `/opt/symetry/nativelib` folder should look like that:

```bash
├── nativelib
│   ├── libblas.so -> libmkl_rt.so
│   ├── libblas.so.3 -> libmkl_rt.so
│   ├── libiomp5.so
│   ├── liblapack.so -> libmkl_rt.so
│   ├── liblapack.so.3 -> libmkl_rt.so
│   ├── libmkl_avx.so
│   ├── libmkl_core.so
│   ├── libmkl_def.so
│   ├── libmkl_gnu_thread.so
│   ├── libmkl_intel_lp64.so
│   ├── libmkl_intel_thread.so
│   ├── libmkl_rt.so
│   └── libsym-gpu.so
```

Additionally To make SymetryML works with GPU perform the following:

1. Download CUDA 10 or 11 from [NVIDIA](https://developer.nvidia.com/cuda-toolkit)
2. Install CUDA, and then use the `nvidia-smi` command to verify that CUDA is working. Example for Centos 7:

```
# download cuda
wget http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda_10.2.89_440.33.01_linux.run

# run the installer
chmod +x cuda_10.2.89_440.33.01_linux.run
./cuda_10.2.89_440.33.01_linux.run

# verify that CUDA was correctly installed
nvdia-smi
```

1. After a successful CUDA installation `nvidia-smi` should output something like:

```
[user@symetry ~]# nvidia-smi
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

1. Be sure that `jetty` user `LD_LIBRARY_PATH` is set correctly.

```
# edit /home/jetty/.bashrc
sudo su jetty
cd
emacs .bashrc

# Add the following lines to /home/jetty/.bashrc
ANT_HOME=/opt/ant
export ANT_HOME
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/opt/symetry/nativelib
export LD_LIBRARY_PATH
```

If you encounter problem please do the following:

1. Use tool like `ldd /opt/system/nativelib/lib-symgpu.so` to pinpoint dynamic any linker problems. Make sure to run the command as the `jetty` user.
2. Make sure the `jetty` user `LD_LIBRARY_PATH` environment variable is correctly set.

### SymetryML Memory Requirements

Please consults the [Technical Requirements](../technical-requirements.md) for more information on memory requirement for various project sizes. Note that with SymetryML, project size is determined by the number of attributes not the number of rows.

### SymetryML Configuration and GPU

As documented in the [Installation Guide](./), the `/opt/symetry/symetry-rest.txt` configuration file contains various properties that alter the SymetryML behavior. The following table lists the properties that are relevant to using SymetryML with NVIDIA GPUs.

| Property                              | Description                                                                                                                                                                                                    |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rtlm.option.rtlm.gpu.matrix.minsize` | Minimum matrix size to use GPU. Matrix operations like multiplication, inversion, etc are used when SymetryML builds models. Other operation like PCA and SVD also can leverage GPU.  Recommended values : 512 |
| `rtlm.option.rtlm.gpu.update.minsize` | Minimum size to use GPU when updating a SymetryML project.  Recommended values:64 to 128                                                                                                                       |
| `rtlm.mgpu.num.gpus`                  | The maximum number of GPUs that can be used on a server in a MultiGPU project.                                                                                                                                 |
| `rtlm.mgpu.runon.one`                 | Enabling that specifies that a MultiGPU project can run on a server with only 1 GPU. '1' enables and '0' disables. Default: '0'                                                                                |
