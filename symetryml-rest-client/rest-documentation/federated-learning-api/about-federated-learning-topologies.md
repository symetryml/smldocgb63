---
description: >-
  This section describes the various network topologies available when building
  federated learning with SymetryML.
---

# Federated Learning Topologies

### Various Topologies Available for Federated Learning with SymetryML

1. **Decentralized**: In a decentralized setting every peer will sync their SymetryML Project with the other peers at some regular periodic interval. Please see [Section Decentralized Federated Learning](about-federated-learning-topologies.md#decentralized-federated-learning) below for an example of a deployment with 3 nodes.
2. **Centralized / Hierarchical**: In a centralized setting a Master aggregator node exists, and only it has a global view on the ‘data’ – again it does not access the data but only aggregates the SymetryML projects of all the individual peers. Please see [Centralized federated learning section](about-federated-learning-topologies.md#centralized-federated-learning) below for details.
3. **Hybrid**: It’s actually possible to compose hybrid topologies, involving combinations of Decentralized & Centralized topologies. Please see [Hybrid Federated Learning sectio](about-federated-learning-topologies.md#hybrid-federated-learning)n below for details.

### Decentralized Federated Learning:

In a decentralized federation each peer shares their PSR with all other peers in the federation via a queuing system. Each peer is able to merge all the PSRs received from fellow peers in the federation and create one merged PSR which now contains all the information from the datasets of its fellow peers in the federation. Each peer is then free to do their own independent modelling from the information in their newly created merged PSR.

In a decentralized network, the administrator is responsible for the following:

* Creating the Federation&#x20;
* Inviting peers to join the Federation via passwords & authentication information&#x20;
* Managing the frequency the Federation will routinely ‘sync’ information (PSRs)
  * Frequency sync options : minutes, hours, days

![figure 1: Decentralized federated learning](<../../../.gitbook/assets/image (1).png>)

### Centralized Federated Learning

In a centralized federation, the Administrator is the only peer in the federation that receives PSRs from other peers, unlike the decentralized network where all peers in the federation send/receive PSRs to/from one another. In a centralized federation the Administrator is the only peer in the federation that is able to merge all PSRs from participants in the federation to create one merged PSR which now contains all the information from the datasets of its fellow peers in the federation. The Admin node is now able to do all the modelling from the merged PSR as though it had access to all the proprietary datasets of the members of the federation.

In a centralized network, the **Administrator** is responsible for the following:

* Creating the centralized Federation&#x20;
* Adding/Inviting peers to join the Federation&#x20;
* The Admin node merged Project updates automatically when peer node project update with new information&#x20;
* Admin node is the only peer in the network that receives Projects from other nodes&#x20;
* Admin node is the only peer in the network able to perform model building on a merged project from the project received from fellow peers in the federation.

Centralized federated learning is called Fusion project with SymetryML. The Rest API for fusion project is documented in the [Fusion Projects section](fusion-projects.md).

![figure 2: Centralized federated learning](<../../../.gitbook/assets/image (4).png>)

### Hybrid Federated Learning

A Hybrid Federation allows an Administrator to build a Federated Learning network with combinations of Centralized and Decentralized federated networks. This allows the Administrator to regulate which peers in the Federation have visibility or do not have visibility to fellow peers PSRs. It allows for managing information sharing, and exclusion of sharing, as desired and/or mandated.

![Hybrid Federation: Example of combination of centralized and decentralized networks](<../../../.gitbook/assets/image (3).png>)

In the example illustrated in Figure 3 we can see that this hybrid structure allows for peers 1, 2, and 3 to get equal access to SymetryML project information from peers 1, 2, and 3, plus access to SymetryML project information of peers 4 and 5 via peer 3. However, in this example, peers 4 and 5 do not receive SymetryML projects from anyone else in the network, they simply share and send their SymetryML projects to be shared with the network. This is just one illustration of a Hybrid Federation, one is able to create any customized Hybrid Federation which can contain several decentralized and centralized federated networks within ones customized Hybrid Federation.
