# Load data to local project

Now that you have created the first node of your federation, the next step will be to learn some data and and to add additional nodes to your federation.

This step is the same regardless of which Federation backend you chose in the first step. Here we will use the AWS backed Federation as an example; however, the process for NATS is identical.

Right click on your **Local Project** and the menu displayed below will appear. Click on **Add Data**:

![Federated Learning: Loading data to local project](<../../.gitbook/assets/fed\_learn\_0.png>)

The next window allows you to select your your data source, whether an existing data source already loaded into SymetryML, or a new data source to be loaded:

![Federated Learning: Selecting data for local project](<../../.gitbook/assets/fed\_learn\_1.png>)

The window below, allows you to review a sample of the data being loaded to ensure the data is valid:

![Federated Learning: reviewing data loaded to local project](<../../.gitbook/assets/fed\_learn\_2.png>)

Next we want to **Learn** the data, in other words we are scanning the local project data to ascertain various descriptive statistics about the data. Right click on your dataset, and the following drop-down menu will appear. Select **Learn**.

![Federated Learning: Learning local data](<../../.gitbook/assets/fed\_learn\_3.png>)

The following window allows you to review all the attributes of your dataset, and make modifications to the data type classifications (_i.e. Continuous, String, Binary, etc…_) if necessary.

![Federated Learning: Review Attribute Types](<../../.gitbook/assets/fed\_learn\_5.png>)

Next, we look at what has been learned about the local data. Double click on the **Exploration** tab and the following screen will appear:

![Federated Learning: Reviewing local data](<../../.gitbook/assets/fed\_learn\_6.png>)

Here you can explore the statistical properties of your data with the various functions (_i.e. Univariate, Correlation, Hypothesis Testing, ANOVA, Chi-Squared, PCA, SVD, Information Gain, VIF_). Additional capabilities of these functions are explained in detail in the [ML Toolkit](../web-documentation/) tutorial.

Finally, double click on the **Federation** icon and press the **Start Pulse** button.
