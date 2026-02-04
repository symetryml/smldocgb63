# Models

After you create a project, you can build models. Using models, you can leverage the historical data learned within the project to make predictions about the future.

## Creating New Models - Main Accordion

To create a new model:

Right-click the **Exploration** icon in the project tree, and then click the appropriate model for you needs.

![Project Create Model](../../.gitbook/assets/project\_create.png)

Assign a new name to the model. Certain models allow for additional options relating to feature selection and model reduction . Furthermore, because these models rely heavily on matrix inversion, the user is able to adjust the sensitivity of such operations via Reciprocal Condition Number Threshold. Note, this feature is for advanced users only.

![Model-Type Selection (Classification and Regression)](../../.gitbook/assets/model\_type\_classification.png)

![Model-Role Selection (Sequence)](../../.gitbook/assets/hmm\_model\_role\_selection.png)

Check the attributes you want to use as inputs and targets. Click **Build Model** to continue.

After completing the model-building process, double click on the model icon to see information about your new model.

## Model Build Parameters

Most of the SML models allow for model specific options to be enabled before building. One example of such option is the **pseudo matrix inverse**. With **pseudo inverse** enabled, a linear model, which would typically not be build due to matrix non invertibility, can be created by the user. However, such model is likely to be unstable and therefore this option should only be used by advanced users.

![Model Build Parameters](../../.gitbook/assets/create\_model\_lda.png)

![Model Build Parameters - LDA](../../.gitbook/assets/create\_model\_lda2.png)

The predefined build parameters will typically have an effect on how the model is fitted. However, the user is free to add additional tags of their own choosing. These will not affect the build process and will simply be treated as additional metadata for the model.

## Model Select

When working with only a handful of attributes, creating models by manually specifying the input attributes is often times sufficient to get an acceptable result. However, as the number of attributes gets large, the choice of which attributes to use can be aided by a model selection heuristic.

The model selection heuristic within SymetryML works as follows. First, the user has to decide on the general type of modelling that needs to be done, whether the task is Classification or Regression. Then, an out of sample data file is provided. SymetryML uses this out of sample file to test the various combinations of input attributes and select the one that provides the highest AUC, when dealing with classification, or the lowest RMSE when dealing with Regression problems.

![Auto Select](../../.gitbook/assets/auto\_select\_0.png)

If the user has a particular algorithm in mind, then the only choice left to be made is one regarding attributes and model hyperparameters. This can be achieved by right clicking on the **Exploration** node picking **Select Model** , choosing **Regression** or **Classification** as the task type and finally picking the model of your choosing.

However, if there are no strong preferences regarding the algorithm used, the user can simply use **Auto Select** option. This will require specifying the task type **Binary Classification** or **Regression**. **Multiclass Classification** will also be available for partitioned projects. SymetryML would then cycle through the appropriate algorithms for the task and pick their hyperparameters accordingly.

![Auto Select - Parameters](../../.gitbook/assets/auto\_select\_1.png)

The available task options are based on the type of project selected. **Binary Classification** and **Regression** will always be available. For **Multiclass Classification** the project must be of type **Partition**.

![Auto Select - Task Types](../../.gitbook/assets/auto\_select\_types.png)

For more information detailing the **Search Heuristic,** **Search Space, Optimization Metric** see section on [SymetryML Concepts - Model Select](symetryml-concepts.md#selecting-models)

## Clone Model

Models within a particular class (i.e., Classification or Regression) can be cloned, rebuilt, with a different algorithm by simply selecting the preferred algorithm within the model info view.

![Model Clone](../../.gitbook/assets/model\_clone.png)

## Sequence Models

Sequence models, unlike their classification and regression counterparts, require the project to be constructed as a sequence project from the start.

## Viewing Model Metadata

Once the model is built, you can double click on the corresponding model icon in the projects view and see the associated metadata.

The type of information displayed is dependent on the class of model.

![Linear Model Metadata](../../.gitbook/assets/linear\_model.png)

![HMM Model Metadata](../../.gitbook/assets/hmm\_meta\_data.png)

![MC Model Metadata](../../.gitbook/assets/mc\_meta\_data.png)

## Refining LDA Models

LDA models have a special property that differentiates them from other algorithms. They can be refined, or updated, in real time. This means you can add or remove attributes as you see fit and the model will be rebuilt instantly. To refine a model, select the **Input Attributes** you want to add and remove the ones you want to remove.

![Example of Refining a Model](../../.gitbook/assets/refine\_model.png)

## Reducing LDA Models

SymetryML can reduce LDA models automatically to find one with the best subset of attributes. This is useful when working with datasets that have many attributes. SymetryML uses a heuristic to build various models, compares the models, and keeps the best one. This comparison is performed efficiently by using the intrinsic values of the models.

![Reducing LDA](../../.gitbook/assets/lda\_reduce.png)

## Deleting Models

To remove a model:

Right-click the model node, and then click **Delete Model**.

![Deleting a Model](../../.gitbook/assets/delete\_model.png)

## Assessing Model Performance

You can assess the performance of most models (excluding MC) using a labeled file.

Right-click the model node, and then click **Assessment**.

![Assessment](../../.gitbook/assets/project\_assessment.png)

Specify the input data source you will use for the assessment, and then click **Next** to continue.

Verify the data source you added, and then click **Next** to continue.

Ensure that your model’s target variables are mapped to the target variables of your file.

![Target Mapping](../../.gitbook/assets/new\_assessment.png)

Click **Finish** to start the assessment process.

After the assessment process finishes, a dialog box asks whether you want to see the assessment results. If you click **Yes**, the assessment information panel appropriate for your model type appears (see the following figures).

* Simple binary classification models show a confusion matrix with lift curve information.
* Regression models show a distribution of errors curve in addition to RMSE and MAE.
* Multi-target classification models show a descriptive confusion matrix.

![Binary Target Assessment](../../.gitbook/assets/assessment\_binary.png)

![Regression Assessment](../../.gitbook/assets/assessment\_regression.png)

![HMM Assessment](../../.gitbook/assets/sml\_hmm\_assess\_5.png)

## Export/Import Models

Once built, a model can be exported out of a project and later imported into a different project. This allows for workflows in which a production environment can be completely separate from a development environment while this levering models build in the latter.

![Model Export](../../.gitbook/assets/model\_export.png)

Importing a previously exported model is as simple as selecting **Import Model** option and specifying the model file.

![Model Import](../../.gitbook/assets/model\_import.png)

![Model Import File Select](../../.gitbook/assets/model\_select.png)

## Using a Model for Predictions

In addition to performing an assessment, you can use an existing model for predictions against a file.

Right-click the model node, and then click **Predict**.

![Model Predict](../../.gitbook/assets/predict\_preview.png)

Specify the input data source, and then click **Next** to continue.

Specify the output data source, which is the location where you want your predictions saved. Click **Next** to continue. A preview of the input data source appears.

Verify that the data source has been read correctly, and then click **Next** to continue.

Ensure the types in the data source match those of the model. Click **Next** to continue.

After the prediction process completes, a sample of the prediction result appears. To download the entire prediction file, click the **Download** button.

![Prediction Download](../../.gitbook/assets/predict\_export.png)
