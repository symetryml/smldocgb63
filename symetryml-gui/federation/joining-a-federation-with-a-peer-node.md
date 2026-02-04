# Joining a Federation with a peer node

The peer node requires federated info encrypted information from the admin node. This information is short lived and must be used to join the federation as soon as possible.

Now we will take a look at the steps required to join an existing federation.

Click on the drop-down menu of **Create Project** and select **Join Federation**:

![Federated Learning: Join Federation](<../../.gitbook/assets/fed\_join\_nats\_00.png>)

Next, the following consecutive windows will appear, detailing all the pertinent information required to join an existing federation, greater detail on the information required follows the figures below:

![Federated Learning: Join Federation](<../../.gitbook/assets/fed\_node2\_join\_aws\_1.png>)

| Get Federation Information | Description                                                                                       |
| -------------------------- | ------------------------------------------------------------------------------------------------- |
| **Project Name**           | This is the name of the local project.                                                            |
| **Project Type**           | This applies to the local project, declare whether the local project is CPU, GPU, Partition, etc. |
| **Project Encoder**        | Optional parameter specifying the encoder to be used.                                             |

Subsequent sections of this guide will cover the above sequence of steps for [AWS](joining-a-federation-with-a-peer-node.md#joining-aws-federation) and [NATS](joining-a-federation-with-a-peer-node.md#joining-nats-federation) respectively.

### **Joining AWS Federation**

![Federated Learning: Join Federation, name local project](<../../.gitbook/assets/fed\_node2\_join\_aws\_2.png>)

| Get Federation Information   | Description                                                                 |
| ---------------------------- | --------------------------------------------------------------------------- |
| **Federation Info**          | The **Encrypted Federation Info**, provided by the admin of the federation. |
| **Federation Info Password** | The **Password**, provided by the admin of the federation.                  |
| **AWS Region**               | The region in which your federation will reside.                            |
| **AWS S3 Bucket**            | The name of the S3 Bucket to receive routine updates from the federation.   |
| **AWS Account**              | This is the ID number of your AWS account.                                  |
| **AWS S3 Access Key**        | The AWS S3 Access Key information.                                          |
| **AWS S3 Secret Key**        | The AWS S3 Secret key information.                                          |

Next, we load data to the local project joining the existing federation. Right click on your local project, and select **Add Data**:

Once you click on **Add Data**, next you select the datasource and data you wish to load:

![Joining Federation: Loading data to local project](<../../.gitbook/assets/fed\_node2\_add\_data\_0.png>)

![Joining Federation: Selecting datasource & data](<../../.gitbook/assets/fed\_node2\_add\_data\_1.png>)

The window below, allows you to review a sample of the data being loaded to ensure the data is valid:

![Joining Federation: verify data](<../../.gitbook/assets/fed\_node2\_add\_data\_2.png>)

Now that your data is loaded, time to **Learn** the data, in other words we are scanning the local project data to ascertain various descriptive statistics about the data.

Right click on the dataset and a drop-down menu will appear, select **Learn**:

![Joining Federation: Learning local data](<../../.gitbook/assets/fed\_node2\_learn.png>)

Next, a window will appear that allows you to verify that your data attributes have been assigned the correct variable type, and allow for changes if necessary:

![Joining Federation: Learn Data & Verify variable type](<../../.gitbook/assets/fed\_node2\_learn2.png>)

Next, we can click on **Exploration** and look at the select descriptive statistics of our local data:

![Exploring data: Univariate Information](<../../.gitbook/assets/fed\_node2\_post\_sync.png>)

Finally, we are ready to join the federation and share our local project with the federation. Double click on **Federation** and the following screen will appear. Here we click **Start Pulse**, and this starts the periodic sharing of information with other peers in the federation, in this example it is every _**Minute**_ as this is what was set by the federation admin, however this frequency can be customized (_i.e. Minute(s), Hour(s), Day(s)_).

![Federation Info](<../../.gitbook/assets/fed\_node2\_pulse\_aws.png>)

### **Joining NATS Federation**

| Get Federation Information   | Description                                                                 |
| ---------------------------- | --------------------------------------------------------------------------- |
| **Federation Info**          | The **Encrypted Federation Info**, provided by the admin of the federation. |
| **Federation Info Password** | The **Password**, provided by the admin of the federation.                  |
| **Project Name**             | The name given to the local project.                                        |
| **NATS Hosts**               | Location of the NATS server                                                 |

![Federated Learning: Join Federation - NATS](<../../.gitbook/assets/fed\_join\_nats\_00.png>)

![Federated Learning:  Join Federation, name local project - NATS](<../../.gitbook/assets/fed\_join\_nats\_1.png>)

![Federated Learning: Join Federation, Federation Info,  Password, host info -  NATS](<../../.gitbook/assets/fed\_join\_nats\_2.png>)

The remainder of the steps (i.e. adding data, learning, and pulsing) for a NATS federation are identical to that of an AWS Federation.

