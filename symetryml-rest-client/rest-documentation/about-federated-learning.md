# About Federated Learning

This section will give background needed in order to understand the business object behind the Federated Learning functionality. The next section [Federated Learning API](federated-learning-api/) will go into the details about the REST API itself.

## Federated Project Terminology

SymetryML projects can easily be merged together. That is, imagine you have 2 projects: (a) a project _p1_ that processed dataset _d1_ and (b) a project _p2_ that processed dataset _d2_. You can merge _p2_ into _p1_ and the resulting _p1_ project will be the same as if _p1_ would have processed the datasets _d1_ and _d2_. This capability is leveraged in a SymetryML Federated project. A federation consists of _n_ Symetry Projects that each process their own private data and share their results at a given interval. This can be seen in the following picture:

![Example of 3 nodes federation](../../.gitbook/assets/fedml-1.png)

In order to fully understand the federated learning REST API one needs to understand a few concepts / terminology.

### Federation Terminology

| Term                                  | Definition                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **peers or node**                     | A node is a member of a federation. It’s basically a Federated Symetry Project.                                                                                                                                                                                                                                                                                                                                                     |
| **federated project**                 | A Federated Symetry Project contains 2 symetry projects. One `local project` and one `federated project`. The federated project is rebuilt from time to time according the the `Federation Schedule` defined by the `federation admin`.                                                                                                                                                                                             |
| **local project**                     | A Federated Symetry Project contains 2 projects. One `local project` and one `federated project`. The `local project` is responsible to process data that is **local** to this project.                                                                                                                                                                                                                                             |
| **Federation**                        | A federation is a set of `nodes` that communicate and share Symetry project information                                                                                                                                                                                                                                                                                                                                             |
| **Federation Info**                   | Information that describes a federation.                                                                                                                                                                                                                                                                                                                                                                                            |
| **Federation Admin**                  | The user who creates a federation automatically becomes the federation admin.                                                                                                                                                                                                                                                                                                                                                       |
| **Federation Contract**               | A set of boolean rules used to enforce quality of individual peer's data. Please see the [Federation Contract section](about-federated-learning.md#federation-contracts) for details.                                                                                                                                                                                                                                              |
| **Federation secret key**             | An AES secret key that is used to encrypt communication between peers/nodes of a federation.                                                                                                                                                                                                                                                                                                                                        |
| **Federation Schedule**               | <p><code>Peers</code> in a <code>federation</code> will send updates to other <code>peers</code> according to a schedule. This schedule is defined by the <code>federation admin</code> when a <code>federation</code> is created. Example of schedule:</p><p>- <strong>m30</strong> : synchronize every 30 mins</p><p>- <strong>h3</strong> : synchronize every 3 hours</p><p>- <strong>d7</strong> : synchronize every 7 days</p> |
| **scheduled synchronization message** | A periodic message sent by a `peer` to other `peers` in a `federation`. The period is defined by the `federation schedule`.                                                                                                                                                                                                                                                                                                         |

A SymetryML Federation can use either Amazon [AWS](https://aws.amazon.com/) services or [NATS](https://nats.io/) in the backend to transmit the various messages to support its functionality.

#### AWS Backed Federation

In the AWS implementation, under the hood, the federation service uses many AWS services:

* Each federation node has an AWS SQS queue to receive messages
* A Federation has an AWS SNS topic that allows fanout messages to be sent to multiple SQS queues.
* Nodes in the federation use messages to the SNS topic to communicate with other nodes
* SNS messages are lightweight and contain pointers to Amazon S3 files that are used to temporarily store message content.
* AWS STS credentials are used to allow other users to access a user’s file on S3.
* The following figure illustrates this:

![Fedederated SML AWS Integration](../../.gitbook/assets/fedml-2.png)

#### NATS Based Federation

NATS based federation use the NATS 'connective technology' to create a federation. For more details on NATS please consult www.nats.io. Under the hood SymetryML uses NATS to send message as well as synchronization message between all the peers in the federation.

![NATS based SML Federation](../../.gitbook/assets/image\_2021-09-09\_151548.png)

Peers can authenticate to the NATS network by either using user/password combination or token. Please consult [https://docs.nats.io/developing-with-nats/security](https://docs.nats.io/developing-with-nats/security) for more details.

## Federated Project Uses Cases

### Create a Federation

The user who creates a federation will become the administrator of it.

### Join a federation

In order to join a federation one must:

1. **Make sure that your clock is correctly synched** using a _ntp_ service or something similar. If a computer’s clock, in a federation, is not correctly synched it will have problems receiving messages from other nodes as the service will ignore many messages because of the discrepancy between the time a message was sent and the internal clock of the computer receiving the message. Those errors could be seen using the [Get Error Log](federated-learning-api/#federated-learning-get-error-log) rest endpoint.
2. Receive `one-time encrypted federation info` along with the password to decrypt the message. This can be done over email, Skype or any other means that allows transferring some base64 encrypted text. The federation administrator can get this encrypted federation info using the [Get Encrypted](federated-learning-api/#federated-learning-get-encrypted-federation-info) rest endpoint
3. Invoke the rest point to join the federation ([FedJoin](federated-learning-api/#federated-learning-join-a-federation)) with the encrypted message and the password received from the federation admin. This message is also to be encrypted using the user `secret key`.
4. Upon successful result from step 3, one can now start syncing with other nodes in the federation. This is done by invoking the [Start Pulse](federated-learning-api/#federated-learning-start-pulse-1) rest endpoint.

## Federation Contracts

SymetryML's federated learning capabilities allow peers to share statistical information without sharing raw data. This shared statistical representation supports:

* supervised and unsupervised machine learning and
* various exploration APIs.

Some of these exploration APIs can be used to enforce quality on the data that peers participating in a federation contribute. Of course, data is never shared directly, only statistical knowledge of the data is shared. But this knowledge is sufficient to enforce rules like: "_enforce that at least 40% of the rows with positive cancer are female_" or "_enforce that at least 500 example of fraud is part of this dataset_", etc...

This enforcement is done via what we call 'Federation Contracts'. A Federation Contract is a list of rules to be enforced on shared statistical data for it to be validated. These rules are effectively Boolean predicates that evaluate to true or false and for a contract to be validated, all its rules need to evaluate to true.

### Federation Contract Rules

Federation Contracts are defined with the following **Backus-Naur** notation as well as the following table that describes the individual function that can be used in a Federation Contract.

#### Federation Contract Backus-Naur Notation

```
RULE_PREDICATE ::= [PredicateRule] [BIN_OP PredicateRule]*

BIN_OP ::= AND | OR

PredicateRule ::= Uni_Rule(FEATURE1) | BI_Rule(FEATURE1, FEATURE2) 

Uni_Rule ::= COUNT  | MEAN | STDDEV | VARIANCE | STDDEV_UNBIASED | VARIANCE_UNBIASED

Bi_Rule = Bi_Exploration_Fct | Conditional_Fct | Complement_Fct | Compose_Fct

Bi_Exploration_Fct ::= COVAR | LINCORR

Conditional_Fct ::= COND_STDDEV | COND_VARIANCE | COND_STDDEV_UNBIASED | COND_VARIANCE_UNBIASED

Complement_Fct ::= COMPL_COND_STDDEV | COMPL_COND_VARIANCE | COMPL_COND_STDDEV_UNBIASED | COMPL_COND_VARIANCE_UNBIASED

Compose_Fct ::= PCT_OF_TRUE | PCT_OF_FALSE | NUM_OCCURENCE_WHEN_TRUE | NUM_OCCURENCE_WHEN_FALSE | MEAN_WHEN_TRUE | MEAN_WHEN_FALSE

```

#### Function That Can Be Used in a Federation Contract

* _**F1** / **F2** means 'Feature 1 type' and 'Feature 2 type'_
* _**C** means Continuous Type_
* B _means binary Type_

<table><thead><tr><th>Rule</th><th width="66">F1</th><th width="64">F2</th><th>Business Rule Interpretation</th></tr></thead><tbody><tr><td><strong>COUNT</strong></td><td>C|B</td><td> </td><td>How many time a feature was seen</td></tr><tr><td><strong>MEAN</strong></td><td>C|B</td><td> </td><td>The mean value of a features</td></tr><tr><td><strong>STDDEV</strong></td><td>C|B</td><td> </td><td>The standard deviation of a features</td></tr><tr><td><strong>VARIANCE</strong></td><td>C|B</td><td> </td><td>the variance of a feature</td></tr><tr><td><strong>STDDEV_UNBIASED</strong></td><td>C|B</td><td> </td><td>The unbiased standard deviation of a features</td></tr><tr><td><strong>VARIANCE_UNBIASED</strong></td><td>C|B</td><td> </td><td>the unbiased variance of a feature</td></tr><tr><td><strong>COVAR</strong></td><td>C|B</td><td>C|B</td><td>The covariance of 2 features</td></tr><tr><td><strong>LINCORR</strong></td><td>C|B</td><td>C|B</td><td>The linear correlation of 2 features</td></tr><tr><td><strong>COND_STDDEV</strong></td><td>C|B</td><td>B</td><td>Stddev of feature 1 when Feature 2 is '1' or true</td></tr><tr><td><strong>COND_VARIANCE</strong></td><td>C|B</td><td>B</td><td>Variance of feature 1 when Feature 2 is '1' or true</td></tr><tr><td><strong>COND_STDDEV_UNBIASED</strong></td><td>C|B</td><td>B</td><td>Unbiased stddev of feature 1 when Feature 2 is '1' or true</td></tr><tr><td><strong>COND_VARIANCE_UNBIASED</strong></td><td>C|B</td><td>B</td><td>Unbiased variance of feature 1 when Feature 2 is '1' or true</td></tr><tr><td><strong>COMPL_COND_STDDEV</strong></td><td> C|B</td><td>B</td><td>Stddev of feature 1 when Feature 2 is '0' or false</td></tr><tr><td><strong>COMPL_COND_VARIANCE</strong></td><td> C|B</td><td>B</td><td>Variance of feature 1 when Feature 2 is '0' or false</td></tr><tr><td><strong>COMPL_COND_STDDEV_UNBIASED</strong></td><td> C|B</td><td>B</td><td>Unbiased stddev of feature 1 when Feature 2 is '0' or false</td></tr><tr><td><strong>COMPL_COND_VARIANCE_UNBIASED</strong></td><td> C|B</td><td>B</td><td>Unbiased variance of feature 1 when Feature 2 is '0' or false</td></tr><tr><td><strong>PCT_OF_TRUE</strong></td><td>B</td><td>B</td><td>Percentage of occurrence with Feature1 is 1 or true and feature2 is '1' or true</td></tr><tr><td><strong>PCT_OF_FALSE</strong></td><td>B</td><td>B</td><td>Percentage of occurrence with Feature1 is 1 or true and feature2 is '0' or false</td></tr><tr><td><strong>NUM_OCCURENCE_WHEN_TRUE</strong></td><td>B</td><td>B</td><td>Number of occurence when Feature1 is 1 or true and feature2 is '1' or true</td></tr><tr><td><strong>NUM_OCCURENCE_WHEN_FALSE</strong></td><td>B</td><td>B</td><td>Number of occurence when Feature1 is 1 or true and feature2 is '0' or false</td></tr><tr><td><strong>MEAN_WHEN_TRUE</strong></td><td>C</td><td>B</td><td>Mean of feature 1 when Feature 2 is '1' or true</td></tr><tr><td><strong>MEAN_WHEN_FALSE</strong></td><td>C</td><td>B</td><td>Mean of feature 1 when Feature 2 is '0' or false</td></tr></tbody></table>

#### Examples of Federation Contract

Here is a small example with the Iris data set. For a Federation Contract to be valid all the rows must evaluate to TRUE.

```
COUNT(sepal_length) >= 150
MEAN(sepal_width) > 3.0
STDDEV(petal_length) > 1.75
VARIANCE(sepal_width) > 0.1
VARIANCE(petal_width) > 0.5
COUNT(Iris_setosa) > 100
COUNT(Iris_versicolor) > 100
COUNT(Iris_virginica) > 100
# BI rules
COVAR(petal_length, petal_width) > 1.28
LINCORR(petal_width, petal_length) > 0.25
COND_COUNT(sepal_length, Iris_versicolor) >= 50
COND_MEAN(sepal_length, Iris_versicolor) >= 50
COND_STDDEV(sepal_length, Iris_versicolor) >= 50
COND_VARIANCE(sepal_length, Iris_versicolor) >= 50
```

Another example using multiple predicates on each line which is permitted per the [Backus-Naur Notation](about-federated-learning.md#federation-contract-backus-naur-notation):

```
COUNT(sepal_length) >= 150 AND MEAN(sepal_width) > 3.0
STDDEV(petal_length) > 1.75
VARIANCE(sepal_width) > 0.1 OR VARIANCE(petal_width) > 0.5
COUNT(Iris_setosa) > 100 AND (COUNT(Iris_versicolor) > 100 OR COUNT(Iris_virginica) > 100)
# BI rules
COVAR(petal_length, petal_width) > 1.28 OR LINCORR(petal_width, petal_length) > 0.25
COND_STDDEV(sepal_length, Iris_versicolor) >= 50 OR COND_VARIANCE(sepal_length, Iris_versicolor) >= 50
```

### Federation Contract Failure Action

Federation Contracts can be evaluated by each peer at two times: First, when sharing their own statistical data with other peers in a federation and second when receiving other peers' statistical data. It's possible to control what SymetryML does when a validation failure occurs at both these times. This is specified when creating / joining a federation by each individual peer by specifying the following parameters inside the **Federation Key Map Value**, please consult the sections mentioned in [Federated Learning: Creating Federation](federated-learning-api/#federated-learning-create-federation) for details:

| Action Type                        | Action Choice                                                                                                                               |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `fed_psr_contract_snd_fail_action` | <ul><li><code>fed_psr_contract_snd_fail_action_block</code> - default</li><li><code>fed_psr_contract_snd_fail_action_allow</code></li></ul> |
| `fed_psr_contract_rcv_fail_action` | <ul><li><code>fed_psr_contract_rcv_fail_action_block</code> - default</li><li><code>fed_psr_contract_rcv_fail_action_allow</code></li></ul> |

## Peer Exploration

Another functionality enabled by SymetryML federated learning is 'peer exploration'. It allows you to use SymetryML's full suite of exploration APIs against the statistical data of a peer. This can be used to perform various univariate and bivariate comparisons between different peers' data without ever seeing the raw data of that peer.&#x20;

If a particular peer wishes to block such functionality please consult [this section](federated-learning-api/#allows-peer-explore-for-a-given-node) to learn how to disable / enable this functionality.

## Secure Multi-Party Computation Mode

SymetryML's federated learning allows sharing certain summary features of the data without ever sharing the raw data. However, in order for the shared statistical representation not to be invertible - that is not allow for the reconstruction of the original data - it needs to have processed a minimum number of rows. This minimum threshold depends on the number of attributes and equals the following:

_**Minimum number of rows = Number of Attributes + 5**_

If this minimum is not met on a given peer at the time of syncing then the peer will not share its current statistical data with the other nodes in a federation. The same logic applies for incremental synchronization. That is the delta of each sync - or the amount of new data since the last synchronization - must follow this rule for the synchronization to be allowed.

This can be a limitation for some federations where each peer does not have lots of data. To circumvent this limitation, it's possible to use secure multi-party computation when peers share their statistical data. The protocol will only complete if the resulting shared data is not invertible.

Federated Learning with SMPC can be enabled by simply adding a key value pair the **`fed_use_smpc`**`= true` inside the **Federation Key Map Value** when an administrator creates a federation  please consult the sections mentioned in [Federated Learning: Creating Federation](federated-learning-api/#federated-learning-create-federation) for details.

## Federated Project REST API at a Glance

Besides creating and joining a federation via rest endpoints, other operations are available. The following table lists all available rest endpoints for federated learning. The following functionality of normal SymetryML projects is available in Federated Project

* [Exploration API](exploration-api.md)
* [Modeling API](modeling-api.md)
* [Prediction API](prediction-api.md)
* [Data Source API](data-source-api.md)
* [WebSocket API](websocket-api.md)

### Limitation of Federated Project

* Features hashing is not available
* By default Random Forest model are disabled. It can be enabled by change the SymetryML server configuration. For details please see the `rtlm.option.sml.fed.strict.mode` key in [Installation guide configuration](../../guides/installation-guide/#about-the-symetryml-configuration) section.
* If your project has more than 2000 attributes you should be careful on how frequently you sync your projects. Please consult the [Federation Terminology](about-federated-learning.md#federation-terminology) section for more information.

| Federated Project Actions                                                                                     | Definition                                                                                                                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**Create a new federation**](federated-learning-api/#federated-learning-create-federation)                   | This rest endpoint is used to create a new federation. The user performing this operation will become the owner of the federation.                                                                                                                                    |
| [**Get Federation Info**](federated-learning-api/#federated-learning-get-federation-info)                     | This is a map of properties for a federation.                                                                                                                                                                                                                         |
| [**Get Encrypted Federation Info**](federated-learning-api/#federated-learning-get-encrypted-federation-info) | Return the federation information encrypted with a password. This is needed in order to share federation information with other peers that the federation admin wants to invite to join the federation. The response will contain a token that can only be used once. |
| [**Join an existing federation**](federated-learning-api/#federated-learning-join-a-federation)               | This rest endpoint allows a peer to join an existing federation.                                                                                                                                                                                                      |
| [**Start Pulsing**](federated-learning-api/#federated-learning-start-pulse)                                   | This endpoint instructs your federated project to start `pulsing`, that is the project will periodically poll for messages from other nodes in the federation as well as sending its scheduled synchronization message.                                               |
| [**Stop Pulsing**](federated-learning-api/#federated-learning-stop-pulse)                                     | Stop synchronizing with the federation                                                                                                                                                                                                                                |
| [**Get Error Log**](federated-learning-api/#federated-learning-get-error-log)                                 | Returns the error log for this project. Since many messages between nodes happen asynchronously, this allows the user to see if there was an error while communicating with the other peers in the federation.                                                        |
| [**Get Sync Log**](federated-learning-api/#federated-learning-get-sync-log)                                   | This returns a log of when this federated project was updated.                                                                                                                                                                                                        |
| [**Get AWS Info**](federated-learning-api/#federated-learning-get-aws-info)                                   | For AWS based federations, this will return information about AWS SNS topic, SNS subscriptions as well as SQS queues. This is for troubleshooting purposes.                                                                                                           |
