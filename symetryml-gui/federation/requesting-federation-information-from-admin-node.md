# Requesting Federation Information from Admin Node

This is the process of requesting a federation invitation from the admin node. The response from the admin is an encrypted message that instructs other members on how to join the federation. The federation invitation is short lived and must be generated for each new node joining the federation.

Now that we have set up our federation, we need to create a **Password** and then get a **Federation Invitation** to share with users that wish to join our federation. Right click on the **Federation** icon and select **Get Federation Info**. Depending on your Federation backend you will be presented with an option for [AWS](requesting-federation-information-from-admin-node.md#requesting-aws-federation-information) or [NATS](requesting-federation-information-from-admin-node.md#requesting-nats-federation-information).

### **Requesting AWS Federation Information**

![Federated Learning: Get Federation Invite.](<../../.gitbook/assets/fed\_info\_0.png>)

Select **Get Federation Info**, and the following screen will appear:

![Federated Learning: Get Federation Invite.](<../../.gitbook/assets/fed\_info\_aws\_1.png>)

Provide the information requested for **Federation Rest Host** and create a password for the federation and populate **Federation Info Password**, select **ENCRYPT**, at which time **Encrypted Federation Info** will display the federation info in an password encrypted form to be shared with other user that the admin wants to invite to the federation. Please note that this encrypted federation information cannot be reused. If you want to invite multiple users, this step needs to be repeated for each user.

| **Get Federation Invite**   |                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Federation Rest Host**                   | Enter the name of the server hosting SymetryML.                                                                                                       |
| **Federation Info Password**               | Create a password to be utilized by the federation being created.                                                                                     |
| **Federation Info Valid Lifespan (Hours)** | Lifespan of Federation Info created (default: 2 hours)                                                                                                |
| **Federation Info**                        | Once **Federation Rest Host** and **Federation Info** are populated, select **ENCRYPT**, and an _encrypted federation information_ will be generated. |

The **Federation Info Password** and **Encrypted Federation Info** will be required by other parties to join the federation. Store this information in a safe place.

### **Requesting NATS Federation Information**

![Federated Learning: Get Federation Invite - NATS](<../../.gitbook/assets/fed\_info\_nats\_1.png>)

| **Get Federation Invite**   |                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Federation Info Valid Lifespan (Hours)** | Lifespan of Federation Info created (default: 2 hours)                                                                                                |
| **Federation Info Password**               | Create a password to be utilized by the federation being created.                                                                                     |
| **Federation Info**                        | Once **Federation Rest Host** and **Federation Info** are populated, select **ENCRYPT**, and an _encrypted federation information_ will be generated. |

The **Federation Info Password** and **Encrypted Federation Info** will be required by other parties to join the federation. Store this information in a safe place.
