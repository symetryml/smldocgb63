# SymetryML Federated Learning

## Introduction

This tutorial is an introduction to the federated learning feature of SymetryML. The objective of federated learning is to build a local SymetryML project which leverages data from across the federation (_i.e. the various member nodes_), while the individual data of each node remains in its local environment, that is it is never being moved or transferred thus preserving data privacy.

Federated SymetryML project can then be used like any other SymetryML project to explore the federated data or to build models locally at each member node of the federation. These locally built models will be identical to one another, and - again - would be identical to-and the equivalent of one model built by putting all the data from all members of the federation into one physical location and then ingesting it into one SymetryML project.

With the SymetryML federated learning network, all data residing at respective local nodes is never moved, confidential information is never shared, data privacy is never compromised, and operates within the parameters set forth by various data governance organizations regarding data privacy protections.

### Prerequisites

This tutorial assumes the reader has some familiarity with the ML Toolkit. The user should feel comfortable creating a project and learning a dataset. If the reader is not familiar with the ML Toolkit, please refer to the [ML Toolkit](../web-documentation/) section. For more information about Federated Learning API refer to the[ About Federated Learning](../../symetryml-rest-client/rest-documentation/symetryml-projects-rest-api.md#about-federated-projects) section of the SymetryML Rest API documentation.

### Supporting Infrastructure

While all proprietary data always resides locally with the respective members of the federation, the co-ordination of messaging (_i.e. sending updates, notifications, etc…_) requires additional infrastructure to enable communications between the various nodes in the network. This supporting infrastructure can be supplied by either Amazon Web Services ([AWS](https://aws.amazon.com/)) or [NATS](https://nats.io/).

In both cases, this supporting infrastructure never sees the raw data, only the PSR representation of the data.

### General Concepts

Regardless of which supporting backend you choose, setting up a federation involves the following four steps:

1. Creating Federation
2. Adding Data To Federation
3. Getting Federation Information
4. Joining a Federation

In the subsequent section, we will be creating a Federation with two different nodes (peers). Each node (peer) will learn their own respective dataset. This will be the publicly available NYC Taxi data. Once we enable pulsing on each node, we will see that the nodes in the Federation will both converge to the same state.
