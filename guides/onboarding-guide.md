# Onboarding Guide

## Introduction

Welcome to SymetryML. This document is to help the user get up and running with SymetryML. This document will go over the minimum technical requirements, data sources and file formats that are compatible with SymetryML, as well as directing the user to more detailed tutorials regarding installation, application, and maintenance of SymetryML.

## SymetryML Platform Options

### Cloud: Amazon Web Services (AWS) Elastic Compute Cloud (EC2)

SymetryML can be deployed on various AWS EC2 instance types, the following table lists some of the recommended instance types which SymetryML can be deployed on. For up to date pricing information regarding cost of the various AWS EC2 instance types, visit AWS EC2.

| AWS Instance Types | vCPU | Memory (GiB) | GPU | GPU Memory (GiB) |
| ------------------ | ---- | ------------ | --- | ---------------- |
| c5.4xlarge         | 16   | 32           | 0   | 0                |
| c5.9xlarge         | 36   | 72           | 0   | 0                |
| c5.18xlarge        | 72   | 144          | 0   | 0                |
| c5.24xlarge        | 96   | 192          | 0   | 0                |
| p2.8xlarge         | 32   | 488          | 8   | 96               |
| p2.16xlarge        | 64   | 732          | 16  | 192              |
| p3.8xlarge         | 32   | 244          | 4   | 64               |
| p3.16xlarge        | 64   | 488          | 8   | 128              |
| p3dn.24xlarge      | 96   | 768          | 8   | 256              |

### On Premise: Technical Requirements

Please consult [Technical Requirements](technical-requirements.md) for details.

## User Interface

### Rest Application Programming Interface (API)

To access SymetryML via Rest API, please consult [REST API Reference Guide](../symetryml-rest-client/rest-documentation/) for details.

The [REST API Reference Guide](../symetryml-rest-client/rest-documentation/) describes the SymetryML REST API. The API lets you create, update, and delete SymetryML projects that can be used for data-mining exploration and predictive modelling.

This reference manual is intended for software developers who write client applications that interact with the SymetryML REST API.

### Graphical User Interface (GUI)

To access SymetryML via the GUI, please consult [ML Toolkit](../symetryml-gui/web-documentation/)[ ](../symetryml-gui/web-documentation/)for details.

The [ML Toolkit](../symetryml-gui/web-documentation/) will give a general overview of SymetryML, including a brief introduction to data-mining and applying its concepts to SymetryML workflow. The core intention of this guide is to provide readers with a brief summary of the information needed to implement SymetryML successfully into their data-driven projects and to cover typical usage scenarios.

## Data Source Connections

### SymetryML Remote Data Source Connections

SymetryML can connect to the following remote data sources:

* AWS Redshift
* AWS S3
* Google Cloud Storage
* Oracle Cloud Storage
* HTTP/HTTPS
* JDBC
* Kafka
* Local
* SFTP
* Spark
* Splunk
* New data sources can be accommodated with SymetryML’s data source plugin interface.

## File formats

### SymetryML supported file formats

* CSV - Comma Separated Value

New file formats can be accommodated with SymetryML’s datasource plugin interface.

## Integrations

### Data Visualization Integrations

#### SymetryML integration with Tableau

Whether accessing SymetryML via the Rest API or the GUI, SymetryML integrates with Tableau API's to support and push output to desired dashboards, reporting and Key Performance Indicators (KPI’s).

## Reference Documents

### SymetryML Guides / Examples

The following is a list of SymetryML resources which the user can access for more detailed information regarding the installation, application, and maintenance of SymetryML.

#### _SymetryML Installation Guide_

The [SymetryML Installation Guide](installation-guide/) will guide you through the install, upgrade and configuration of the SymetryML suite of applications.

#### _SymetryML Examples_

The following URL contains links to sample code: [_SymetryML Examples_](https://github.com/symetryml/symetryMLv5.4).

#### _SymetryML Admin Users Guide._

The [SymetryML Admin CLI](untitled.md) is a command-line application that allows administrators to manage service updates and user credentials. This user guide describes the common usage patterns for the SymetryML Admin CLI.

###
