# Federated Data & Modelling

Depending on your choice of the **Sync Schedule** you might have to wait a few minutes for the two nodes to sync up.

Because of the asynchronous nature of fed synchronization that happens in the background, it is important to check for errors by clicking on the _Error Log_ icon, as well as the the _Sync Log_ icon for general sync information.&#x20;

Once the nodes are in sync, it is now time to see how the federation allows sharing of these local projects so that every peer can each build one unified federated project. Afterwards, we will take a look at how individual nodes of the same federation are able to build identical models.

Let us take a look at Node 2:

Here is a view of the project of Node 2 prior to joining the federation:

![Node 2: Local project prior to joining Federation](<../../.gitbook/assets/fed\_node2\_presync.png>)

Below is a view of the project of Node 2 after joining the federation and sharing/receiving projects from the federation (in this example only one other peer in the federation).

![Node 2: project after joining Federation](<../../.gitbook/assets/fed\_node2\_post\_sync.png>)

The first noticeable difference is that the **COUNT** increased from a local count of _10,000_ to a federated **COUNT** of _20,000_. _**Node 1**_ has a **COUNT** of _10,000_ and _**Node 2**_ has a **COUNT** of _10,000_, hence the federated **COUNT** is now totalling _20,000_ as demonstrated in the figure above. Once again, the remaining statistics (_i.e. Mean, Variance, Std. Dev, and Skewness_) now reflect the total from all peers in the federation.

Because syncronisation across federation happen at the same time, we can be sure that Node 1 has the same statistics for the shared variables.

### Federated Learning Model Building

Now that peer/node 1 has synced with other peers in the federation, we are ready to demonstrate how individual peers in a federation are able to build models. For further information on how to create models in the web UI of SymetryML, you can reference the following document [SML Web User Guide model section](../web-documentation/symetryml-concepts.md#models). The following is a simple example of how to build models on any peer of a federation:

Left click on **Exploration**, and you will be presented with a drop down menu with various options. Select **Create Model**, which will present another drop-down menu where you can choose what class of model you wish to build. In this case we are building a multi-linear regression model, hence we are going to select **Regression** and **MLR**.

![Node 1: Model Building](<../../.gitbook/assets/fed\_node1\_model\_0.png>)

The next screen requires you to name your model:

![Federation Node 1: Name the model](<../../.gitbook/assets/fed\_node1\_model\_1.png>)

Next we select our **Input** and **Target** variables, and when complete we select **Build Model**:

![Federation Node 1: Select Input & Target variables, and build the model](<../../.gitbook/assets/fed\_node1\_model\_2.png>)

Next we can left click on the model name, in this case **Node1\_MLR**, and a drop-down menu will appear where we select **Model Info**, or we can simply double click on the model name and the **General Model Info** will be presented (_i.e. Model Name, Model Type, # of Attributes, Time to build model(s), # Models built_).

Next, we navigate to the grey pane at the bottom of the page, and click on the portion labelled **Additional Model Info**, which will initiate the blue pane to rise up, revealing additional model information, and select **Coefficient Stats**:

![Federation Node 1: Model Parameters, Additional Model Info (Coefficient Stats)](<../../.gitbook/assets/fed\_node1\_model\_3.png>)

Following the same steps for _**Node 2**_, which is also part of this federation, we arrive at the same last step, as presented for _**Node 1**_ above, to reveal the model parameters of the **MLR** model built on _**Node 2**_ :

![Federation Node 2: Model Parameters, Additional Model Info (Coefficient Stats)](<../../.gitbook/assets/fed\_node2\_model\_3.png>)

As we can see, both peers produce identical models in that they produce the exact same model parameters even though both models were built separately in their respective local SymetryML projects. All SymetryML's project in each peer of a federation eventually converge when they synchronize thus allowing each peer to build identical models.
