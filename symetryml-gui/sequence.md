# Sequence Models

## Introduction

Sequence Analysis describes a chain of events, or states, to provide a concise summary of the sequence-generating process. This summary allows us to predict the next likely state in the sequence, determine the likelihood of occurrence for a particular sequence, and generate a new sequence having the same statistical properties as the original.

### Related Documents

For a brief introduction to data-mining concepts, as well as general guidance on navigating the ML Toolkit, please refer to the respective [guide](web-documentation/).

## Sequence Models and Datasets

### Markov Chain

A Markov Chain (MC) is a graphical representation of a sequence of states and the relationship among them. At its most basic, an MC model shows the probability of transitioning from one state to another.

### Hidden Markov Model

Like the MC, a Hidden Markov Model (HMM) is a graphical model describing the probabilities of transitioning from one state to another. It extends the Markov process by focusing on two set of states:

* Observed
* Hidden

The observed states, as their names imply, are visible at any given time. While hidden states are not visible directly, the user knows about their existence. The most common use case for HMM is to infer the sequence of underlying hidden states given a set of visible states.

### Sequence Dataset

In the context of SymetryML, the output of a sequence generating process is represented as a dataset containing an array of states in discrete time.

For a MC model, the sequence can be as simple as an array of comma-separated values.

In the following figure, the sequence represents a daily weather pattern, which we will learn.

![Sequence Data](../.gitbook/assets/seq\_data\_weather.png)

Hidden Markov Models require two sets of sequences:

* One for the hidden state.
* One for the observed state.

We will use the activity dataset, which contains a labeled sequence of human activity (the hidden state), and the readings from various sensors around the room.

![Sequence Data - Activity](../.gitbook/assets/seq\_data\_activity.png)

### Markov Chain

To work with MC models:

Create a new project of type **Sequence**.

![](../.gitbook/assets/sml\_p1.png)

Select the **weather.seq** file as your data source.

![](../.gitbook/assets/sml\_p3.png)

Verify the format of the file.

![Data Source Selection](../.gitbook/assets/sml\_p2.png)

Verify the types. For this example, the absence of a header in the CSV file forced the program to generate header names automatically. Leave the attribute types as String. Click **Finish** to continue.

![Data Type Mapping](../.gitbook/assets/sml\_p4.png)

After the project is learned, the following univariate statistics appear.

![Univariate Statistics](../.gitbook/assets/sml\_p\_univariate.png)

Click **Build Model > Sequence > Markov Chain**.

Click a name, and then click **Finish**.

![Build MC Model](../.gitbook/assets/sml\_p\_create\_0.png)

After the model is built, you can view its properties by double-clicking the corresponding model icon.

![MC Model Info](../.gitbook/assets/sml\_p\_create\_2.png)

Prediction works similar to every other model. You select the input file that you want to predict and the destination file where the prediction will be written.

The result should resemble the following:

![MC Model Prediction](../.gitbook/assets/sml\_p\_prediction.png)

In this figure:

* **res\_Result** = the next most likely step in the sequence.
* **prob\_Result** = the probability of that res\_Result.
* **seq.p\_Result** = the likelihood of the sequence given your MC Model.

### Hidden Markov Model

To work with a Hidden Markov model:

Create a new project. Verify that the project type is **Sequence**.

![Sequence Project](../.gitbook/assets/sml\_hmm\_p0.png)

Select the input data source.

![Data Source Selection](../.gitbook/assets/sml\_hmm\_p1.png)

Verify your data.

![Data Source Verification](../.gitbook/assets/sml\_hmm\_p2.png)

Convert the attribute types to **Categorical**.

![Type Conversion](../.gitbook/assets/sml\_hmm\_p3.png)

Click **Finish** to complete the wizard.

After the learning finishes, you should see the following attributes in the univariate statistics.

![Project Trained](../.gitbook/assets/sml\_hmm\_p5.png)

### HMM Model

To build the HMM model:

Click **Create Model > Sequence > HMM**.

![HMM Create](../.gitbook/assets/sml\_hmm\_model\_0.png)

Enter a name for the model.

![Model Name](../.gitbook/assets/sml\_hmm\_model\_1.png)

Select the appropriate category for the hidden and the observed state. Click **Build Model** to start the model build process.

![State Selection](../.gitbook/assets/sml\_hmm\_model\_2.png)

After the model is complete, you should see the following **Model Info**.

![HMM Model Info](../.gitbook/assets/sml\_hmm\_model\_3.png)

You can now use your HMM to predict the sequence of hidden states given the set of observed states

Right-click the model icon, and then click **Predict**.

Select an input data source.

![Input selection](../.gitbook/assets/sml\_hmm\_pred\_0.png)

Select and output data source.

![Output selection](../.gitbook/assets/sml\_hmm\_pred\_1.png)

Verify that your data and the type mapping are valid.

![Data Verification](../.gitbook/assets/sml\_hmm\_pred\_2.png)

![Type Selection](../.gitbook/assets/sml\_hmm\_pred\_3.png)

The output of the prediction should be the **pred\_Result** column, a sequence of inferred hidden states, and the columns from the original file.

![HMM Prediction Result](../.gitbook/assets/sml\_hmm\_pred\_4.png)

To perform an assessment:

Right-click the model, and then click **Assessment**.

![Assessment Menu](../.gitbook/assets/sml\_hmm\_assess\_0.png)

Select the input Data Source. In this example, we’re using a smaller subsample of the original data source.

![Data Selection](../.gitbook/assets/sml\_hmm\_assess\_1.png)

Validate that your data is valid.

![Data Preview](../.gitbook/assets/sml\_hmm\_assess\_2.png)

Verify the correct type mapping of attributes.

![Type Mapping](../.gitbook/assets/sml\_hmm\_assess\_3.png)

Be sure there is a match between the model attribute name and the name in the test file. Click **Finish** to start the assessment job.

![Target Mapping](../.gitbook/assets/sml\_hmm\_assess\_4.png)

After the assessment completes, you should see a confusion matrix that compares the predicted hidden sequence to the actual sequence present in the file.

![Assessment Result](../.gitbook/assets/sml\_hmm\_assess\_5.png)
