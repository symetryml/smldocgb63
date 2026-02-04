# REST API Reference Guide

This reference manual describes the SymetryML REST Application Programming Interface (API) that is exposed from SymetryML Inc. The API lets you create, update, and delete SymetryML projects that can be used for data-mining exploration and predictive modeling.

## Audience

This reference manual is intended for software developers who write client applications that interact with the SymetryML REST API. This reference manual provides documentation of all API operations, which return raw JSON. For optimal utilization of network bandwidth, the SymetryML REST API supports the consumption and creation of JSON data structures only, unless you use a data source from the [Data Source API](data-source-api.md).

## Assumptions

This reference manual assumes that you have a general understanding of RESTful Web Services along with development tools, environments, and IDEs necessary to test and create an application in your programming language of choice. It also ensures that only users with the private key can make requests of those services.

Users of the SymetryML REST service must be authorized by SymetryML Inc.

## Reference Implementation

A reference implementation of the rest client for python (pymetry) is available [here](https://sml-pymetry-soft.s3.amazonaws.com/pymetry-5.6.1515a0.tar.gz).

Installation of the python script can be performed by download the latest version of pymetry rest client and running the following command:

```
pip install pymetry-6.1.XXXXX.tar.gz
```

## Terminology

The following table defines key terms related to the SymetryML REST API.

.

| Term                   | Definition                                                                                                                                                                                                                                                                                                                         |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SymetryML Projects** | Allow creation and deletion of SymetryML projects, learning and forgetting new data, and obtaining information about SymetryML projects.                                                                                                                                                                                           |
| **Data source**        | Data sources are abstractions of CSV files that can be accessed through Amazon S3, SFTP, HTTP/HTTPS and other communication APIs.                                                                                                                                                                                                  |
| **Encoder**            | In data mining, the process of transforming a categorical attribute into a continuous attribute is called _encoding._ Within SymetryML, an encoder object allows the user to transform high-cardinality attributes into meaningful data points that are more predictive than their original form for a particular target attribute |
| **Data exploration**   | Data mining and statistical exploration of SymetryML projects.                                                                                                                                                                                                                                                                     |
| **Model building**     | Allows building models using various modeling algorithms, such as linear discriminant analysis (LDA), multiple linear regression (MLR), and quadratic discriminant analysis (QDA).                                                                                                                                                 |
| **Model prediction**   | Allows to make predictions using models.                                                                                                                                                                                                                                                                                           |
