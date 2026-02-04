# Technical Requirements

Copyright © 2021 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved April 15th, 2021

## Technical Requirements

### Technical Requirement for SymetryML

| Requirement        | Description                                                |
| ------------------ | ---------------------------------------------------------- |
| Operating System   | Centos 7.x or Amazon Linux Based on Red Hat 7.x            |
| Application Server | Certified with Jetty 9.4.49                                |
| Additional Drivers | CUDA version 10 or up                                      |
| GPU                | NVIDIA Tesla or GPU with minimum compute capability of 3.0 |
| Spark              | Spark 2.4.x                                                |
| Java               | Java 11                                                    |
| Redis              | Redis 2.8.x and up                                         |

### System Recommendations

| Recommendation              | Description                               |
| --------------------------- | ----------------------------------------- |
| Spark Cluster worker memory | Minimum: 8 GB Recommended: 16 GB and more |

### GPU Support

| GPU Support  | Description       |
| ------------ | ----------------- |
| CUDA library | CUDA Version 10.x |
| Intel MKL    | Version 11.0+     |

## SymetryML Memory Requirements

SymetryML memory requirements depend on the number of projects and the number of attributes in each project. The following table provides a rough guideline as to the compute and memory requirements for your projects. These numbers are for dense datasets. For sparse datasets, the number will vary.

| Number of Attributes | Type of Project          | RAM Memory Requirement |
| -------------------- | ------------------------ | ---------------------- |
| 10                   | CPU                      | 512m                   |
| 64                   | CPU                      | 512m                   |
| 128                  | CPU or GPU               | 1g                     |
| 512                  | GPU                      | 1g                     |
| 1024                 | GPU                      | 1g                     |
| 4096                 | GPU or multi-GPU         | 4g                     |
| 8192                 | GPU or multi-GPU         | 16g                    |
| 12,500               | GPU or multi-GPU         | 24g                    |
| 20,000-25,000        | multi-GPU                | 64g                    |
| 25,000-100,000       | multi-GPU on Nvidia V100 | 64G to 256G            |

## Data Source Requirements

| Data Source        | Description                                                                                                                                                                                                                                                                      |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| JDBC / ODBC        |                                                                                                                                                                                                                                                                                  |
| S3                 |                                                                                                                                                                                                                                                                                  |
| SFTP               |                                                                                                                                                                                                                                                                                  |
| HTTP / HTTPS       |                                                                                                                                                                                                                                                                                  |
| Local File         | Local file where the application server resides                                                                                                                                                                                                                                  |
| Kafka Streams      |                                                                                                                                                                                                                                                                                  |
| RedShift           |                                                                                                                                                                                                                                                                                  |
| Plugins            | It's possible to add new 'plugin' data source to SymetryML                                                                                                                                                                                                                       |
| SymetryML Rest API | One can use the SymetryML Rest API to push data into a SymetryML project. Please consult the [following section for details](../symetryml-rest-client/rest-documentation/symetryml-projects-rest-api.md) (section called _Real Time Streaming of Data into SymetryML Projects_). |
