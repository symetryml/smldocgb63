# Streams

Unlike other Data Sources, streams cannot be created independent of a project. The option to add a stream only exists when you:

* Have an existing SymetryML project
* Create a new SymetryML project from scratch

In both cases, creating a stream would require you to specify at minimum the following information:

* **Bootstrap Servers**
* **Schema Registry**
* **Kafka Topic**

![Stream Info](../../.gitbook/assets/add\_stream.png)

**Time btw. Persists** is an optional parameter that controls the frequency at which SymetryML persists a project with a stream. Default value of 300 seconds, should be sufficient unless your schema contains a large number of attributes (1000+).

**Start from Beginning** checkbox would read the earliest possible data from the stream. Unselecting this option would only update the project with the data that will be generated from this point in time.

Advanced Kafka parameters could be enabled specified by clicking the **Kafka Options** button and adding the corresponding parameter/value pair.

## Adding a Stream to an existing Project

Right-click the project node, and then click **Add Stream**.

Specify the required stream info (bootstrap servers, topic, and etc.)

When choosing a stream topic, the topic names will be displayed in the following format : **TOPIC\_NAME:NUM\_PARTITIONS**. Ensure the correct partitions have been selected by clicking **Kafka Options** button and verifying the prefilled **kafka.partitions** field.

![Stream Topic Browser](../../.gitbook/assets/add\_partitions0.png)

![Stream Parameters](../../.gitbook/assets/add\_partitions1.png)

Ensure your information is valid and click **Next**

Verify the stream in the preview panel. Click **Next** to continue.

In the final panel specify the correct type mapping and click **Finish**

## Creating a Project with Stream

When creating a new project, adding a stream can be performed by simply:

1. Selecting **New Data Source** on the data selection panel
2. Specifying the required stream info (bootstrap servers, topic, and etc.)

See [Creating New Project](projects.md#creating-new-projects) for more information.

## Stopping and Restarting a Stream

Once added to a project, a stream will be continuously polled and will cause the project to be updated automatically with new incoming data. This behavior can be paused and restarted again.

![Stream Actions](../../.gitbook/assets/stream\_options.png)

To stop the update behavior:

1. Right-click on the stream node
2. Click **Stop Stream**

While this does stop the project from being updated, it has no effect on the underlying stream. The Kafka Stream will continue to ingest new data in the background.

Resuming project update behavior can be done in one of two ways:

* A stream can be resumed from the point where it was paused (**Resume Stream**)
* It can be restarted from the very beginning. (**Start from Beginning**)

The correct restart point will depend on the architecture of your Kafka cluster. If the cluster allows for storage of large volume of data, simply resuming might be the best option. On the other hand, if the data in the cluster is changing rapidly, restarting from the beginning might be more optimal.

## Stream Metrics

Information about a Kafka Stream can be obtained by right clicking on the stream node and selecting **Stream Metrics**

![Stream Metrics](../../.gitbook/assets/stream\_metrics.png)

## Stream Errors

**Error Log** allows the user to diagnose any potential problems that might occur when interfacing with a Kafka Stream. This is the first place you should look when troubleshooting.

![Stream Error Log](../../.gitbook/assets/stream\_errors.png)
