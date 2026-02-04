# Installation Guide

\`Copyright © 2023 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved May 15th, 2023

## Introduction

This guide describes how to install, upgrade and configure the SymetryML suite of applications. For simplicity, the application comes prepackaged with Jetty 9.4.49. For additional information, refer to the following documents:

* [SymetryML Technical Requirements](../technical-requirements.md)
* [Installation Guide - Spark](spark-installation-guide.md)
* [Installation Guide - GPU](gpu-installation-guide.md)
* [ML Toolkit](../../symetryml-gui/web-documentation/)

### Files in the SymetryML Package

The following table lists the files in the SymetryML-6.1.tar.gz package.

| Type   | Name                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------ | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| File   | jetty                               | `/etc/init.d/` Linux service file.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Folder | jetty-distribution-9.4.49.v20220914 | Contains a fully functioning Jetty application with pre-installed SymetryML application in the webapps folder.                                                                                                                                                                                                                                                                                                                                                                                                                 |
| File   | README.txt                          | List all files in this release.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| File   | Release-Notes.txt                   | Release Note.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| File   | symetry.tar.gz                      | <p>Archive file that contains support files needed by SymetryML. It should normally be decompressed in the <code>/opt/symetry</code> directory. Those support files are needed to:<br></p><p>- communicate with a Spark cluster.<br>- support native GPU support<br>- use SymetryML with Python</p><p>- run a Symetry Kafka Stream Application<br></p><p>Please consult the installation guide for <a href="spark-installation-guide.md">Spark</a> and <a href="gpu-installation-guide.md">GPU</a> for additional details.</p> |

## Installation

### Assumptions

This section assumes you have the following files:

* SymetryML package: `SymetryML-{VERSION}-tar.gz`
* SymetryML license file: `sym.lic`.
* Redis with version greater or equal to 2.8.19 installed and available at port 6379 - or available on another computer
  * please see `rtlm.option.rtlm.db.redis.host` and `rtlm.option.rtlm.db.redis.port` in [About the SymetryML Configuration ](./#about-the-symetryml-configuration)section for additional configuration help.
* Java JDK 11 is installed.

### Installation Procedure

After making sure that the base [requirements](../technical-requirements.md#technical-requirements) are covered, perform the following procedure:

1. Decompress SymetryML-6.1-tar.gz.
2. Add Jetty user:
   * `useradd jetty`
   * `usermod -aG wheel jetty`
3. Add to .bashrc in /home/jetty
   * `export JAVA_HOME=/opt/jdk`
   * `export PATH=$JAVA_HOME/bin:$PATH`
   * `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/symetry/nativelib:/usr/local/cuda/lib64`
4. cp `jetty-distribution-9.4.49.v20220914` to /opt/.
5. Allowing jetty to write logs in the sub directory:
   * `chown -R jetty:jetty jetty-distribution-9.4.49.v20220914`
6. Create a symbolic link to `jetty-distribution-9.4.49.v20220914` named `/opt/jetty`.
7. From root directory, decompress the `symetry.tar.gz` file and ensure its contents are in `/opt/symetry`. Please consult the _**directory tree located at the bottom**_ _**of this Installation**_\*\* **\_**Procedure\*\*\_ to ensure your `/opt/symetry` resembles the directory tree illustrated.
8. Edit /opt/jetty/start.ini to specify where your SymetryML license resides. Add a line similar to the following:
   * `-Dsym.lic.loc=/opt/symetry/sym.lic`
9. Move the service file jetty to /etc/init.d
   * `mv jetty /etc/init.d/`
10. Add Jetty to the list of Linux services using chkconfig
    * `chkconfig jetty on`
11. Start Jetty:
    * `sudo service jetty start`

Your `/opt/symetry` folder content should resemble the following:

```
├── lib
│   └── sym-spark-assembly.jar
├── libExt
│   ├── (...) MANY ..jars file needed by SymetryML Rest, please do not modify
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
├── plugins
│   ├── activation-1.1.1.jar
│   ├── csv2.dsplugin
│   └── ds-plugin-csv2.jar
├── python
│   ├── SMLDataFrameUtil.py
│   └── SMLProjectUtil.py
├── spark-support
│   ├── spark2.4.5
│   │   └── lib -> /opt/spark-2.4.5-bin-hadoop2.7/jars
│   ├── spark2.4.6
│   │   └── lib -> /opt/spark-2.4.6-bin-hadoop2.7/jars
│   └── spark3.0.1
│       └── lib -> /opt/spark-3.0.1-bin-hadoop2.7/jars
│   └── spark3.0.2
│       └── lib -> /opt/spark-3.0.2-bin-hadoop2.7/jars
├── sym-kafkastream-app
│   ├── etc
│   │   ├── log4j-f.properties
│   │   └── simple.props
│   ├── lib
│   │   ├── MANY ..jars file needed kafka stream application, please do not modify
│   └── run-ks-app-linux.sh
├── symetry-admin-cli.jar
├── symetry-rest.txt
├── symetry-web.txt
└── symetry.sh
```

## Updating an Existing System

Make sure to use same version of jetty for this release of SymetryML. That is `jetty-distribution-9.4.49.v20220914`.

1. Decompress SymetryML-6.1-tar.gz.
2.  Stop Jetty:

    ```
    sudo service jetty stop
    ```
3. cp `{SymetryML-6.1.tar.gz}/jetty-distribution-9.4.49.v20220914`/ to `/opt/jetty-distribution-9.4.49.v20220914` 3.1. Create a symbolic link `/opt/jetty` that points to `/opt/jetty-distribution-9.4.49.v20220914`
4.  Decompress the `symetry.tar.gz` file and make sure to put its content into `/opt/symetry`. Your `/opt/symetry` folder content should be like the following:

    ```
    ├── lib
    │   └── sym-spark-assembly.jar
    ├── libExt
    │   ├── (...) MANY ..jars file needed by SymetryML Rest, please do not modify
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
    ├── plugins
    │   ├── activation-1.1.1.jar
    │   ├── csv2.dsplugin
    │   └── ds-plugin-csv2.jar
    ├── python
    │   ├── SMLDataFrameUtil.py
    │   └── SMLProjectUtil.py
    ├── spark-support
    │   ├── spark2.4.5
    │   │   └── lib -> /opt/spark-2.4.5-bin-hadoop2.7/jars
    │   ├── spark2.4.6
    │   │   └── lib -> /opt/spark-2.4.6-bin-hadoop2.7/jars
    │   └── spark3.0.1
    │       └── lib -> /opt/spark-3.0.1-bin-hadoop2.7/jars
    │   └── spark3.0.2
    │       └── lib -> /opt/spark-3.0.2-bin-hadoop2.7/jars
    ├── sym-kafkastream-app
    │   ├── etc
    │   │   ├── log4j-f.properties
    │   │   └── simple.props
    │   ├── lib
    │   │   ├── MANY ..jars file needed kafka stream application, please do not modify
    │   └── run-ks-app-linux.sh
    ├── symetry-admin-cli.jar
    ├── symetry-rest.txt
    ├── symetry-web.txt
    └── symetry.sh
    ```
5.  Restart Jetty:

    ```
    sudo service jetty restart
    ```

## About the Jetty Configuration

| File                   | Description                                                                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/etc/init.d/jetty`    | Controls the Java context used to launch the Jetty application server. For instance, to modify the minimum and maximum memory used by the Java virtual machine (JVM) and change the garbage collector behavior, modify `JAVA_OPTIONS` with the following configuration: `JAVA_OPTIONS="-server -Xms4096m -Xmx8192m -Dfile.encoding=UTF-8"` Notes: Modify the `-Xms4096m` `-Xmx8192m` according to your project size. |
| `/opt/jetty/start.ini` | This is the file where you need to specify where your SymetryML license is located. You need to specify it using a java property like the following: `-Dsym.lic.loc=/opt/symetry/sym.lic` If this is not specified SymetryML will assume a default location of `/opt/symetry/sym.lic`.                                                                                                                               |

## SymetryML Memory Requirements

Please consults the [SymetryML Technical Requirements](../technical-requirements.md) for more information on memory requirements for various project sizes. Note that with SymetryML, project sizes are limited by the number of attributes, not the number of rows.

## SymetryML REST Configuration

SymetryML has a configuration file in `/opt/symetry/symetry-rest.txt`. The following table provides information about available parameters.

| Parameter                                                     | Description                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `jobreaper.run.period`                                        | Controls how often the ‘thread reaper’ runs. The thread reaper removes finished jobs from the job queue. Normally, a job is removed after a REST call is made to inquire about a job’s status. If this call never comes, the job reaper ensures that the queue does not grow too large.                                                                                                       |
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

Certain Symetry Web parameters can be configured by editing the `/opt/symetry/symetry-web.txt` configuration file. Ensure that `-Drtlm.web.db=/opt/symetry/symetry-web.txt` is set in `/opt/jetty/start.ini`.

| Parameter                    | Description                                          |
| ---------------------------- | ---------------------------------------------------- |
| `sml.web.legal.notice`       | Set to `false` to hide the legal notice during login |
| `sym.web.auto.logoff.enable` | Set to `false` disable auto logoff                   |
| `sym.web.auto.logoff.time`   | Auto logoff time in minutes                          |

## Troubleshooting

**Question:** I am getting the following error: `java.util.concurrent.ExecutionException: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target`

**Answer:** The host where your jetty is does not have a valid certificate. If you know you can trust this server you can bypass this by setting `sml.fed.admin.trust_all_certs=1` as documented in the [About the SymetryML Configuration](./#about-the-symetryml-configuration) section.
