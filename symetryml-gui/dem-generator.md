# DEM Generator

## Introduction

The DEM Generator allows users to generate DEMs locally on their workstation and upload/merge them to a remote SymetryML project.
This is especially useful if the dataset of interest cannot leave the user's workstation.

A remote project must exist on the SymetryML server before using the DEM Generator.
For information on how to create a remote project, please see this [guide](./web-documentation/projects.md#creating-new-projects).


## Tutorial

Successful use of the DEM Generator involves the following three steps:

1. Specifying a remote project
2. Processing a local file
3. Uploading the generated DEM to the remote SymetryML Server

![DEM Generator](../.gitbook/assets/dem\_gen\_0.png)

The remainder of this tutorial will guide you through these three steps.



### Step 1 Specifying a remote project

Referencing a remote project can be done in one of two ways.

* [Manual Project Reference](dem-generator.md#manual-project-reference) - Manually inputing the remote project information
* [Automatic Project Reference](dem-generator.md#automatic-project-reference) - Downloading the reference file which contains remote project information

#### Manual Project Reference

To specify a remote project, start the DEM Generator and click on **Select a Destination** button.

Input the following information:

* Hostname - SymetryML URI
* Username - SymetryML User ID
* Secret Key - SymetryML Secret Key

Refresh the project list and select the remote **Project** as your destination.

![Remote Project](../.gitbook/assets/dem\_gen\_1.gif)

Selecting the remote project will allow you verify it's schema.

Press **Confirm** to finish Step 1.

#### Automatic Project Reference

Download the project reference file

![Remote Project Reference](../.gitbook/assets/dem\_gen\_1b0.gif)

Start the DEM Generator and click on **Select a Destination** button.

Drag and drop the reference file

![Remote Project Reference](../.gitbook/assets/dem\_gen\_1b1.gif)

Input the password you used to generate the reference file.

Press **Confirm** to finish Step 1.

### Step 2 Processing local file

After the remote project has been specified, it is time to choose a local file which will be converted into a DEM.

This can be done by either clicking on the **Select a File** button, or simply dragging the file into the DEM Generator

![Local Data](../.gitbook/assets/dem\_gen\_2.gif)

You will be presented with a file preview. After previewing the file, you click **Next** to move to the type mapping view where you will confirm that the attributes types have been assigned correctly. Right clicking on the attribute allows you to change its type.

Click **Confirm** to close the type mapping panel and finish Step 2.


### Step 3 Uploading the DEM

At this stage you have specified a destination project, generated a local DEM, the only step left is click the **Upload** button.

![DEM Upload](../.gitbook/assets/dem\_gen\_3.gif)

Log back into your ML Toolkit environment and view the end result.

![Project Sync](../.gitbook/assets/dem\_gen\_final.gif)



## Advanced Project Options

Advanced project options (e.g., random forest, logistic regression, etc...) should be specified by the remote project during the project creation step. DEM Generator will then mimic those properties locally. If the remote project is empty, the DEM Generator will allow you to specify them locally. This can be done during [Step 2](dem-generator#step-2-processing-local-file), by selecting **Next** on the type mapping panel.


![DEM Advanced Project Options](../.gitbook/assets/dem\_gen\_adv.gif)

For more information on the advanced project options, please see the following section [section](./web-documentation/projects.md#advanced-project-features).
