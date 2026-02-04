# Fusion Projects

## About Fusion Projects

Fusion projects bring 2 major functionalities to the SymetryML environment

1. Allows to scale to real time streaming data with a very large number of IO (Gigabytes per secs)
2. Allows to create centralized federated learning setting where the data privacy of each cells in preserved. Please consult [About Federated Learning Topologies](about-federated-learning-topologies.md) for more details.

### Real Time Streaming Data Scaling

Fusion Project allows to handle data coming from a stream with very high throughput. It is possible to scale the system to handle millions of rows / tuples per seconds. SymetryML Fusion projects do not process any data per say, instead they are reliant on other projects (called _cell projects_) to process streaming data in parallel. This is shown here:

![Example of 3 Cells Fusion Project](../../../.gitbook/assets/fusion-1.png)

When a request is made to [Model API](../modeling-api.md) or [Exploration API](../exploration-api.md) fusion project will take care of interrogating its _cell projects_ to get the latest data in a _just in time_ fashion. Under the hood, the Fusion project refresh the data when needed, but no less that a pre-defined number of seconds. The default value is 15 seconds but can easily be adjusted with the `rtlm.option.sml.fusion.prefetch.min.time.ms` config parameter of the _symetry-rest.txt_ configuration file. Please consult the [Installation Guide](../../../guides/installation-guide/) for details.

One important thing to know about Fusion project is that all components (the main fusion project and its _cell projects_ should reside relatively close to each other in order to minimize network latency. Ideally they should reside in the same rack mount.

### Fusion Project and Federated Learning

A Fusion Project also allow to create a centralized federated learning setting where only the a central server can leverage data from various site without never accessing the actual data. That is privacy of data is preserved. In the image before the 3 cells could be 2 individual doctor offices + 1 laboratory situated at different location in a country. These 2 doctor and laboratory would be feeding data to a central server - possibly a larger organization that oversees model building and then distribute them back to the doctors and laboratory organizations.

Again please consult [About Federated Learning Topologies](about-federated-learning-topologies.md) for more details on how fusion can be used to create a centralized federated learning system.

## Fusion Projects REST API

The following section describes the rest endpoint pertaining to fusion projects. Please note that once a Fusion project is created almost all of the other functionality of _normal_ SymetryML project is available for this project. We say **almost** available because some functionalities are not available:

* Data source cannot be attached to a Fusion project because the processing of data needs to happen on the _project cells_ of the fusion project
  * Data source statistics are also not available for same reason

On the other hand its possible to

* Use the [Exploration API](../exploration-api.md)
* Use the [Model API](../modeling-api.md) and [Prediction API](../prediction-api.md)
* All other project lifecycle endpoints like deleting a project, renaming, info, etc.

## Create New SymetryML Fusion Project

This API function call create a new SymetryML Fusion project.

### URL

```
POST /symetry/rest/{cid}/projects/fusion/create [Encrypted [FusionCellInfoList]]
```

### Create New Fusion SymetryML project Body

The body of the request consist of an encrypted [FusionCellInfoList](../appendix-a-json-data-structure-schema.md#fusioncellinfolist). It is mandatory and contains login information to access the REST-API of each cells.

### Create Project Query Parameters

| Parameter   | Required / Optional | Description                                                              |
| ----------- | ------------------- | ------------------------------------------------------------------------ |
| **pid**     | Required            | Name of the new SymetryML project.                                       |
| **persist** | Optional            | Whether to persist or not a project. Valid values are  \* true  \* false |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                       |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **201**          | CREATED             | Success.  `{"statusCode":"CREATED","statusString":" + SYMETRYML Created with id:r1","values":{}}`                                                 |
| **409**          | CONFLICT            | SymetryML project already exists.  `{"statusCode":"CONFLICT","statusString":" + Customer [c1] already have SYMETRYML with id[r1], ","values":{}}` |

## Fusion Project Cell Status About Specific Cells

Request status for a specific list of cells.

### URL

```
POST /symetry/rest/{cid}/projects/fusion/{pid}/cellsStatus [body=Encrypted FusionCellInfoList]
```

### Fusion Cell Body

The body of the request consist of an encrypted [FusionCellInfoList](../appendix-a-json-data-structure-schema.md#fusioncellinfolist). It contains login information to access the REST-API of each cells.

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                     |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                        |
| **400**          | BAD REQUEST         | SymetryML project does not exist.  `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

### HTTP Response Entity

| HTTP Response Entity | Description                                                                                                                                                      |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cellsStatusEncrypted | The json string representation of a [FusionCellStatusList](../appendix-a-json-data-structure-schema.md#fusioncellstatuslist) encrypted with the user secret key. |

## Update Cells Information of a Fusion Project

This REST endpoint allows to update the list of cells for a Fusion project.

### URL

```
POST /symetry/rest/{cid}/projects/fusion/{pid}/updateCellsInfo [body=Encrypted FusionCellInfoList]
```

### Create New Fusion SymetryML project Body

The body of the request consist of an encrypted [FusionCellInfoList](../appendix-a-json-data-structure-schema.md#fusioncellinfolist). It contains login information to access the REST-API of each cells.

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                     |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                        |
| **400**          | BAD REQUEST         | SymetryML project does not exist.  `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |
