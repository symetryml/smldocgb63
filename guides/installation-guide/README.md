# Installation Guide

Copyright © 2026 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved

## Introduction

SymetryML is delivered as a Docker image with all dependencies pre-configured, including the application server, Redis, native math libraries, and Spark support. This guide describes how to install, update, and configure SymetryML. For additional information, refer to the following documents:

* [SymetryML Technical Requirements](../technical-requirements.md)
* [Installation Guide - Spark](spark-installation-guide.md)
* [Installation Guide - GPU](gpu-installation-guide.md)
* [ML Toolkit](../../symetryml-gui/web-documentation/)

## Prerequisites

Before installing SymetryML, ensure the following are in place:

* **Docker Engine** installed on the host machine.
* **SymetryML license file** (`sym.lic`) provided by Symetry.
* (Optional) **NVIDIA Container Toolkit** if using GPU or Multi-GPU projects — see the [GPU Installation Guide](gpu-installation-guide.md).
* (Optional) **External Spark 4.1.0 cluster** — see the [Spark Installation Guide](spark-installation-guide.md).

## Pulling the Docker Image

SymetryML is hosted on Amazon ECR. To pull the image, first authenticate with the registry, then pull.

**Step 1 — Authenticate with ECR**

Use the AWS CLI to obtain an authentication token and log in to Docker:

```bash
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  428117700962.dkr.ecr.us-east-1.amazonaws.com
```

> If you do not have AWS CLI access, contact Symetry at support@symetryml.net for an authentication token.

**Step 2 — Pull the image**

```bash
docker pull 428117700962.dkr.ecr.us-east-1.amazonaws.com/sml-server:6.3.0
```

## Running SymetryML

The recommended way to run SymetryML is with Docker Compose. Create a `docker-compose.yml` file:

```yaml
services:
  sml-server:
    image: 428117700962.dkr.ecr.us-east-1.amazonaws.com/sml-server:6.3.0
    container_name: sml-server
    ports:
      - "8080:8080"
      - "8443:8443"
    volumes:
      - ./sym.lic:/opt/symetry/sym.lic:ro
    environment:
      - JAVA_OPTS=-server -Xms4096m -Xmx8192m -Dfile.encoding=UTF-8
      - SML_RTLM_DB_REDIS_HOST=localhost
      - SML_RTLM_DB_REDIS_PORT=6379
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped
```

Start SymetryML:

```bash
docker compose up -d
```

### Ports

| Port | Protocol | Description         |
| ---- | -------- | ------------------- |
| 8080 | HTTP     | Web UI and REST API |
| 8443 | HTTPS    | Secure access       |

### GPU Support

To enable GPU acceleration, add the NVIDIA runtime and GPU environment variables:

```yaml
services:
  sml-server:
    image: 428117700962.dkr.ecr.us-east-1.amazonaws.com/sml-server:6.3.0
    container_name: sml-server
    runtime: nvidia
    ports:
      - "8080:8080"
      - "8443:8443"
    volumes:
      - ./sym.lic:/opt/symetry/sym.lic:ro
    environment:
      - JAVA_OPTS=-server -Xms4096m -Xmx8192m -Dfile.encoding=UTF-8
      - NVIDIA_VISIBLE_DEVICES=all
      - LD_LIBRARY_PATH=/usr/local/cuda/lib64:/opt/symetry/nativelib
      - SML_RTLM_DB_REDIS_HOST=localhost
      - SML_RTLM_DB_REDIS_PORT=6379
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped
```

See the [GPU Installation Guide](gpu-installation-guide.md) for additional details.

### Redis

Redis is bundled inside the Docker image and starts automatically. To use an external Redis instance instead, update the `SML_RTLM_DB_REDIS_HOST` and `SML_RTLM_DB_REDIS_PORT` environment variables to point to your external Redis server.

## Configuration

### Customizing Configuration Files

SymetryML uses two configuration files that control REST API and Web UI behavior:

* `symetry-rest.txt` — REST API configuration (see [SymetryML REST Configuration](#symetryml-rest-configuration))
* `symetry-web.txt` — Web UI configuration (see [SymetryML Web Configurations](#symetryml-web-configurations))

To override the default configuration, mount custom files into the container:

```yaml
volumes:
  - ./sym.lic:/opt/symetry/sym.lic:ro
  - ./symetry-rest.txt:/opt/symetry/symetry-rest.txt:ro
  - ./symetry-web.txt:/opt/symetry/symetry-web.txt:ro
```

### SymetryML Memory Requirements

Please consult the [SymetryML Technical Requirements](../technical-requirements.md) for more information on memory requirements for various project sizes. Note that with SymetryML, project sizes are limited by the number of attributes, not the number of rows.

Memory is configured via the `JAVA_OPTS` environment variable. Adjust `-Xms` (minimum heap) and `-Xmx` (maximum heap) according to your project size:

```yaml
environment:
  - JAVA_OPTS=-server -Xms4096m -Xmx8192m -Dfile.encoding=UTF-8
```

### SymetryML REST Configuration

SymetryML REST behavior is controlled by the `symetry-rest.txt` configuration file. The following table provides information about available parameters.

| Parameter                                                     | Description                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `jobreaper.run.period`                                        | Controls how often the 'thread reaper' runs. The thread reaper removes finished jobs from the job queue. Normally, a job is removed after a REST call is made to inquire about a job's status. If this call never comes, the job reaper ensures that the queue does not grow too large.                                                                                                       |
| `jobreaper.job.expiration`                                    | Expiration time for a job to be removed from the list of jobs after it is finished. That is removed by the JobReaper mentioned above.                                                                                                                                                                                                                                                         |
| `request.signature.timeout`                                   | Reserved for internal use.                                                                                                                                                                                                                                                                                                                                                                    |
| `rtlm.option.assessment.limit.size`                           | The maximum number of lines to read from a datasource during a model assessment. Default: 1000000                                                                                                                                                                                                                                                                                             |
| `rtlm.option.azure.blob.inputstream.chunk.size.max.bytes`     | When using Azure Blob storage datasource, this is the chuck size used when downloading the files. Default is 4194304.                                                                                                                                                                                                                                                                         |
| `rtlm.option.emr.listener.host`                               | IP address to use when receiving results from EMR job.                                                                                                                                                                                                                                                                                                                                        |
| `rtlm.option.job.scheduler.fixed.thread.max.pool.size`        | Number of jobs that can run in parallel. Setting this number too high might cause contention problems on the computer running SymetryML and might cause Jetty to become unresponsive. Recommended values: 1 or 2.                                                                                                                                                                             |
| `rtlm.option.rtlm.core.max`                                   | Maximum number of CPU cores to use when updating a Symetry Project with data. This number cannot be higher than the number authorized in your SymetryML license.                                                                                                                                                                                                                              |
| `rtlm.option.rtlm.db.redis`                                   | Specifies whether to use redis. 0 = disables persistence altogether.                                                                                                                                                                                                                                                                                                                          |
| `rtlm.option.rtlm.db.redis.host`                              | Hostname of the redis server. Normally, this is localhost and was never tested otherwise.                                                                                                                                                                                                                                                                                                     |
| `rtlm.option.rtlm.db.redis.master.name`                       | Reserved for internal use.                                                                                                                                                                                                                                                                                                                                                                    |
| `rtlm.option.rtlm.db.redis.port`                              | Redis port (default: 6379).                                                                                                                                                                                                                                                                                                                                                                   |
| `rtlm.option.rtlm.db.redis.sentinel`                          | Reserved for internal use.                                                                                                                                                                                                                                                                                                                                                                    |
| `rtlm.option.rtlm.db.redis.sentinel.host`                     | Reserved for internal use.                                                                                                                                                                                                                                                                                                                                                                    |
| `rtlm.option.rtlm.db.redis.sentinel.port`                     | Reserved for internal use.                                                                                                                                                                                                                                                                                                                                                                    |
| `rtlm.option.rtlm.gpu.matrix.minsize`                         | Minimum matrix size to use GPU. Matrix is used when SymetryML builds models.                                                                                                                                                                                                                                                                                                                  |
| `rtlm.option.rtlm.gpu.update.minsize`                         | Minimum size to use GPU to update SymetryML project.                                                                                                                                                                                                                                                                                                                                          |
| `rtlm.option.rtlm.mgpu.runon.one`                             | Enabling that specifies that a MultiGPU project can run on a server with only 1 GPU. '1' enables and '0' disables. Default: '0'                                                                                                                                                                                                                                                               |
| `rtlm.option.rtlm.mgpu.num.gpus`                              | The maximum number of GPUs that can be used on a server in a MultiGPU project.                                                                                                                                                                                                                                                                                                                |
| `rtlm.option.rtlm.model.mc.maxinfo`                           | Maximum target count or transition size in a Markov Chain or Hidden Markov Model. Default: 512                                                                                                                                                                                                                                                                                                |
| `rtlm.option.sml.autoselect.num.threads`                      | The number of worker threads to use during Auto Select. Default: 8                                                                                                                                                                                                                                                                                                                            |
| `rtlm.option.sml.batch.predict.limit.size`                    | The maximum number of lines that can be read in a batch prediction. Default: 1000000                                                                                                                                                                                                                                                                                                          |
| `rtlm.option.sml.date.server.timeout`                         | The maximum time in ms allowed for fetching the current time from the date server during licence verification. Default: 10000                                                                                                                                                                                                                                                                 |
| `rtlm.option.sml.default.rcond.threshold`                     | The reciprocal condition number tolerance in matrix inverse operations. Default: 1e-14                                                                                                                                                                                                                                                                                                        |
| `rtlm.option.sml.density.num.threads`                         | Control the number of threads that perform density computation                                                                                                                                                                                                                                                                                                                                |
| `rtlm.option.sml.disable_bet.update`                          | Enabling for the disabling of updates to the BET. '1' enables and '0' disables. Default: '0'                                                                                                                                                                                                                                                                                                  |
| `rtlm.option.sml.explore.double.format`                       | Control the formatting of result returned by the Exploration API. Default value is `%.4f`                                                                                                                                                                                                                                                                                                     |
| `rtlm.option.sml.fed.admin.token.default.timeout.hours`       | The default number of hours that federation token are valid, that is the number of hours that a user has to join a federation once the admin generate their encrypted federation information. Default is 2 hours                                                                                                                                                                              |
| `rtlm.option.sml.fed.admin.trust_all_certs`                   | This option should be used with caution. It will allows one to join a federation setup by an admin node for which its https certificate is not to be trusted. For mode details please consult the Federated Learning section from the REST API reference at the following url: [SML\_REST\_API\_Reference\_Guide](../../symetryml-rest-client/rest-documentation/about-federated-learning.md) |
| `rtlm.option.sml.fed.strict.mode`                             | Enabling for strict mode, which prevents Federated projects from using a Random Forest or Kaplan Meier model. '1' enables and '0' disables. Default: '0'                                                                                                                                                                                                                                      |
| `rtlm.option.sml.fed.thread.pool.size`                        | internal                                                                                                                                                                                                                                                                                                                                                                                      |
| `rtlm.option.sml.fusion.prefetch.min.time.ms`                 | Minimum time to wait between Fusion cells prefetch.                                                                                                                                                                                                                                                                                                                                           |
| `rtlm.option.sml.matrix.allow.non.native`                     | 1 to allow. 0 to block. Default is 0. This control whether or not SymetryML will allow the software to run without the optimized 'native' library. By default if SymetryML cannot load those native libraries it won't be able to build models.                                                                                                                                               |
| `rtlm.option.sml.mgpubet.persistence.dir`                     | The directory to store the persisted MultiGPU project file into. Default: '/tmp'                                                                                                                                                                                                                                                                                                              |
| `rtlm.option.sml.mgpubet.persistence.suffix`                  | The suffix to identify the persisted MultiGPU project file. Default: '-BET'                                                                                                                                                                                                                                                                                                                   |
| `rtlm.option.sml.mgpubet.persistence.file-ext`                | The file extension of the persisted MultiGPU project file. Default: '.bet.gz'                                                                                                                                                                                                                                                                                                                 |
| `rtlm.option.sml.power.prefix`                                | Default is `pt_`                                                                                                                                                                                                                                                                                                                                                                              |
| `rtlm.option.sml.power.separator`                             | Default is `^`                                                                                                                                                                                                                                                                                                                                                                                |
| `rtlm.option.sml.spark.core.max`                              | Maximum number of CPU cores to use in a job on a Spark cluster.                                                                                                                                                                                                                                                                                                                               |
| `rtlm.option.sml.spark.so.send`                               | Spark Cluster driver socket configuration. For details, refer to the linux socket documentation.                                                                                                                                                                                                                                                                                              |
| `rtlm.option.sml.spark.so.rcv`                                | Spark Cluster driver socket configuration. For details, refer to the linux socket documentation.                                                                                                                                                                                                                                                                                              |
| `rtlm.option.sml.streams.error_on_df_change`                  | Whether or not to throw an error when the schema of stream change as new data arrive. Default is 0 for false.                                                                                                                                                                                                                                                                                 |
| `rtlm.option.sml.streams.processor.num.threads`               | Number of threads to use to process SML Streams. Default is 4.                                                                                                                                                                                                                                                                                                                                |
| `rtlm.option.sml.streams.processor.sleep.time.ms.empty`       | Time in millisecs to sleep when there are no activity on a Stream Processor. Default is 5000, (5 secs).                                                                                                                                                                                                                                                                                       |
| `rtlm.option.sml.streams.processor.sleep.time.ms.with_record` | Time in millisecs to sleep when there are activity on a Stream Processor. Default is 0 millisecs.                                                                                                                                                                                                                                                                                             |
| `rtlm.option.sml.task.max`                                    | Maximum number of task running at the same time. For now only affect how many SymetryML Kafka stream application that can be launched by the server.                                                                                                                                                                                                                                          |
| `rtlm.option.sml.user.projects.max`                           | Maximum number of project allowed                                                                                                                                                                                                                                                                                                                                                             |
| `rtlm.option.sml.user.datasources.load.max`                   | Maximum number of data sources, default is 1000.                                                                                                                                                                                                                                                                                                                                              |
| `rtlm.option.sml.version.key`                                 | Reserved for internal use. Do not change.                                                                                                                                                                                                                                                                                                                                                     |
| `rtlm.option.sml.version.host`                                | Reserved for internal use. Do not change.                                                                                                                                                                                                                                                                                                                                                     |
| `rtlm.option.spark.automl.sample.random.seed`                 | Seed for AutoML with Spark. Used for seeding the randomizer used to sample the data used for the autom warmup dataset. Default is 42.                                                                                                                                                                                                                                                         |
| `rtlm.option.spark.job.process.jvm.heap.size.min`             |                                                                                                                                                                                                                                                                                                                                                                                               |
| `rtlm.option.spark.job.process.jvm.heap.size.max`             | Default is `4096m`                                                                                                                                                                                                                                                                                                                                                                            |
| `rtlm.option.spark.job.process.jvm.heap.size.min`             | Default is `32g`                                                                                                                                                                                                                                                                                                                                                                              |
| `rtlm.option.spark.listener.host`                             | IP address to use when the Spark Cluster sends results.                                                                                                                                                                                                                                                                                                                                       |
| `rtlm.option.vfs.http.connection.timeout`                     | HTTP connection timeout in ms for HTTP datasources. Default: 30000                                                                                                                                                                                                                                                                                                                            |
| `rtlm.option.vfs.http.so.timeout`                             | HTTP socket timeout in ms for HTTP datasources. Default: 30000                                                                                                                                                                                                                                                                                                                                |

#### SymetryML Web Configurations

Certain SymetryML Web parameters can be configured by editing the `symetry-web.txt` configuration file.

| Parameter                    | Description                                          |
| ---------------------------- | ---------------------------------------------------- |
| `sml.web.legal.notice`       | Set to `false` to hide the legal notice during login |
| `sym.web.auto.logoff.enable` | Set to `false` disable auto logoff                   |
| `sym.web.auto.logoff.time`   | Auto logoff time in minutes                          |

## Updating SymetryML

To update SymetryML, pull the new image version and recreate the container:

```bash
docker pull 428117700962.dkr.ecr.us-east-1.amazonaws.com/sml-server:<NEW_VERSION>
```

Update the image tag in your `docker-compose.yml`, then restart:

```bash
docker compose down
docker compose up -d
```

## Troubleshooting

**Question:** I am getting the following error: `java.util.concurrent.ExecutionException: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target`

**Answer:** The host does not have a valid SSL certificate. If you know you can trust this server you can bypass this by setting `sml.fed.admin.trust_all_certs=1` in the `symetry-rest.txt` configuration file. See the [SymetryML REST Configuration](#symetryml-rest-configuration) section for details.
