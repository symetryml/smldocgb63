# Installation Guide - Spark

Copyright © 2026 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved

## Introduction

SymetryML is delivered as a Docker image with Spark 4.1.0 support pre-configured. All required libraries, jars, and configuration files are included in the container — no manual Spark setup is needed inside the SymetryML Docker image.

This guide covers the requirements for connecting SymetryML to an external Spark cluster.

## Prerequisites

* A working Spark 4.1.0 cluster accessible from the SymetryML Docker container.
* Network connectivity between the SymetryML container and all Spark master and worker nodes.

## System Requirements

| Requirement                 | Description                                                                                                                                                                                                      |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark Cluster               | Spark 4.1.0 cluster accessible from the SymetryML Docker container.                                                                                                                                             |
| Spark Master                | 24 to 32 cores computer with high-speed Internet connection.                                                                                                                                                     |
| Spark Cluster worker memory | Minimum: 8 GB Recommended: 16 GB Start the number of workers on your node based on the amount of worker memory. For example, on Amazon S3: \* c5.8xlarge instance: 8 workers. \* c5.4xlarge instance: 4 workers. |

## GPU Support on Spark Workers

If you plan to use SymetryML GPU or Multi-GPU projects on your Spark cluster, each worker node must have NVIDIA GPU support:

* NVIDIA GPU with Compute Capability >= 3.5
* CUDA drivers installed and verified (run `nvidia-smi` to confirm)

For detailed GPU setup instructions, refer to the [Installation Guide - GPU](gpu-installation-guide.md).

## Spark FAQs

**Question:** What does the following error message mean: `ERROR 500: INTERNAL_SERVER_ERROR : Cannot assign requested address`.

**Answer:** Be sure the SymetryML configuration files (/opt/symetry/symetry-rest.txt) has the rtlm.option.spark.listener.host set correctly to your host.

**Question:** What does the following error message mean: `java.lang.OutOfMemoryError: GC overhead limit exceeded`.

**Answer**: Increase your worker memory using spark configuration parameters.

**Question:** What does the following error message mean: `15/08/17 17:43:47 ERROR WorkerWatcher: Error was: akka.remote.InvalidAssociation: Invalid address: akka.tcp://sparkWorker@boson.local:49991`

**Answer:** This error is most likely caused by lack of memory so, verify worker logs and increase your worker memory.

**Question:** I see `[java.net.BindException: Address already in use` message in my log.

**Answer:** You can usually ignore this message.
