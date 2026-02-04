# SymetryML Projects REST API

## SymetryML Projects Life-Cycle

SymetryML project are different than _classical_ machine learning or other data science projects in the way data is ingested and in how machine learning models are created. Normally one would create a machine learning model by specifying some file(s) or data structure containing the data description - number of rows, input attributes, target(s) for models. This step can be highly memory intensive if the data to be processed is large.

SymetryML is different. There is no limitation on the number of rows that can be processed and the number of rows do not impact the memory requirement to build a model. Updating a project with some data and building a machine learning model based on a project are 2 separates operations:

* When updating a project - that is learning or forgetting some data - internally some representation of the data is being updated. This update is usually quite fast. This internal representation can be queried in real time using the [Exploration API](exploration-api.md).
* When building a model, the internal representation is used to extract values needed by each of the different machine learning models supported by SymetryML. This makes model building very quick, usually under a few milli-seconds. If another model is needed perhaps with different inputs and/or target(s), one just needs to issue a request to build another model and no _re-scanning_ of the data is needed. Models are built based on the current state of a project. If a project was updated with some data, a rebuild of the model might be needed so that the models use the latest data available in the project.

## SymetryML Project Type

New with SymetryML version 5.0 are “Federated” project types. The following table describes the difference between these project types and when to use them. For more details on Federated Project please consult this [section](symetryml-projects-rest-api.md#about-federated-projects).

| Project Type          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **cpu or dynamic**    | <p>- Project update (learn/forget) done on CPU.</p><p>- Project stored on CPU memory.</p><p>- Use this type of project if you have categorical attributes or fewer than 50-100 attributes.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **gpu**               | <p>- Project update (learn/forget) done on GPU.</p><p>- Project stored on GPU memory.</p><p>- Use this type of project if you have data with more than 100 - 200 attributes.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **multi gpu**         | <p>- Project update (learn/forget) done on multiple GPUs.</p><p>- Project stored on multiple GPUs memory.</p><p>- Use this type of project if you have dense data with more than 1000 attributes and you have multiple GPUs available. Please note that you cannot directly use category with a GPU project type. Such data needs to be one hot encoded before pushing it into a SymetryML GPU project.</p><p>- Multi GPU project are stored in the local file system. Where they are saved depends on the following SymetryML configuration parameters (Consult the<a href="../../guides/installation-guide/#about-the-symetryml-configuration"> Section on SymetryML configuration in the Installation Guide</a> for details)</p><ul><li><code>sml.mgpubet.persistence.dir</code></li><li><code>sml.mgpubet.persistence.suffix</code></li><li><code>sml.mgpubet.persistence.file-ext</code></li></ul> |
| **Sequence**          | SymetryML needs to perform some additional logic for these type of models to be built. Use the _sequence_ project type so that you can build such models on your data. See the section [About Sequence Projects](symetryml-projects-rest-api.md#about-sequence-projects) for more details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **Partitioned**       | Use this type of project if you need to use LDA model with multi-class classifier or if you need to build a QDA model. When creating such projects one needs to specify the attribute that will be used to partition the project. This attribute needs to be of type `String` and usually should be the target for building your LDA or QDA models.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **Federated Project** | Federated Project allows multiple SymetryML projects to form a _federation_ that leverage each individual project so that they all work together as if they were one single project. That is from the perspective of each individual project they can all build the same models or compute the same exploration metrics and all of this is accomplished without sharing any data. For more information please consult the [Federated Project Section](symetryml-projects-rest-api.md#about-federated-projects).                                                                                                                                                                                                                                                                                                                                                                                         |
| **Fusion Project**    | Fusion Project allows to handle data coming from a stream with very high throughput. Please consult the section on [Fusion Project ](federated-learning-api/fusion-projects.md#about-fusion-projects)for details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |

## SymetryML Projects and Random Forest

New With SymetryML 5.2 is a powerful Online Random Forest model. In order to use it, some configuration is needed when creating your project:

* Specify the target / dependent column
* Specify the type of random forest (currently only classifier is available, soon regressor random forest will also be available)
* Various Hyper parameters. Please see the [RF Models](modeling-api.md#about-random-forest) for details about the additional configuration available.

## SymetryML Projects and Kaplan Meier

Kaplan Meier(KM) is a survival model which can estimate a survival function from lifetime data. Unlike most SymetryML models, KM cannot be built after a project has learned a dataset. Instead, the user must specify the following parameters when creating a project.

* Specify the time column.
* Specify the event column.
* Specify the group column.
* Please see the [KM Models](modeling-api.md#about-kaplan-meier-model) for details about the additional configuration available.

## Clustering

New with SymetryML version 5.4 is an online version of KMeans clustering. The clustering algorithm is controlled by a few parameters that need to be specified when creating a project. Please consult the [Cluster Models](modeling-api.md#about-clustering) for details on the additional configuration to enable KMeans clustering on your project.

## Partitioned Projects

New with SymetryML 4.2 is the ability to create _Partitioned Projects_. Partitioned project allows you to build Multi-Class LDA models as well as QDA models. When creating such projects one must specify which attributes will be the targets for these models. This parameter is called the _partition column_. This parameter is mandatory. Please refer to this [section](symetryml-projects-rest-api.md#sample-request-response-partitioned-projects) for a sample REST request / response.

## Sequence Projects

With the release of SymetryML Version 4.1 came the ability to use Markov Chains and Hidden Markov Model. To build such models, you create a sequence SymetryML Project and specify its order. Order defines your _ngrams_ that will be learned and the level of you _ngrams_ will be a given value in a dataframe. For Markov chains, specify a categorical attribute for the input of your model. For the Hidden Markov Model, the observed state can be categorical or continuous, but the hidden must be categorical.

For the sequence project, SymetryML interprets the [dataframe](appendix-a-json-data-structure-schema.md#dataframe-json) data structure differently:

* If a [Dataframe](appendix-a-json-data-structure-schema.md#dataframe-json) to be processed has only 1 attribute, it will be assumed that each line represent a token or level of a sequence.
* If a DataFrame to be processed has more than 1 column, it will be assumed that each line represent a sequence to learned.

For more information on _ngrams_, order, and level, see [https://en.wikipedia.org/wiki/N-gram](https://en.wikipedia.org/wiki/N-gram).

## Federated Projects

New with version 5.0 of SymetryML is the capabilities to allows multiple SymetryML projects - possibly on different sites / geographical locations / different business units - to synchronize with each other in order to leverage each others data without sharing the actual data. This functionality is called `Federated Learning`. A federated project act exactly the same as any other project in terms of how you build model or explore the data. The only difference is how you create them. For more details about the lifecycle of federated project please consult the [Federated Learning API](federated-learning-api/).

## Data Filtering

It's possible to filter data as it is consumed by a SymetryML project. In order to filter out some tuple one needs to pass a string containing a comma separated list of _predicates_ that all need to be true for a row / tuple to be processed, otherwise it will be ignored.

Example of such string:

```
sml_project_predicate_row_filter:Iris_setosa==1;sepal_width_b2==1
```

The following table list the available binary boolean operator that can be used in any individual predicates.

| Boolean Operator | Description       | Notes                             |
| ---------------- | ----------------- | --------------------------------- |
| ==               | Equals            | Works on both Strings and Numbers |
| >=               | Greater or Equals | Only works with Numbers           |
| <=               | Smaller or Equals | Only works with Numbers           |
| !=               | Not Equal         | Works on both Strings and Numbers |
| >                | Greater           | Only works with Numbers           |
| <                | Smaller           | Only works with Numbers           |

## Create New SymetryML project

This API function call create a SymetryML project.

### URL

```
POST /symetry/rest/{cid}/projects?pid={new-project-id} [body=KSVSMap]
```

### Create New SymetryML project Body

The body of the request consist of a key/value map. It is optional and depends on the type of project. Please refer to the following table that describes mandatory parameters for the different type of projects. Also refer to [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap) for detail on the request body json data structure.

| Optional Project Parameters              | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **sml\_project\_autosave**               | boolean | Default is **true**. This parameter controls whether or not a project is automatically saved after new data is pushed into it. The default behavior is **true**. If **false** is used, it is important to invoke the [Save](symetryml-projects-rest-api.md#saving-a-symetryml-project) endpoint to persist the project in order to not lose information between server restart.                                                                                                                                                                                                                                                                                                                                                                                                                |
| **sml\_project\_learn\_merge**           | boolean | <p>Default is <strong>false</strong>. This parameter is used whenever a project learns new data, see <a href="symetryml-projects-rest-api.md#learn-new-data-incremental-update">Update</a> for details. This configuration allows to <code>commit</code> new data to a project only if the whole <a href="appendix-a-json-data-structure-schema.md#dataframe-json">DataFrame</a> was processed without an error. Whenever new data is pushed to a SymetryML project the following happens:<br></p><p>1. A new temporary project is created<br>2. The new temporary project is updated with the the data.<br>3. If no problem is encountered the temporary project is merged with the main project.<br>4. This allows a <strong>commit all or nothing</strong> approach to processing data.</p> |
| **sml\_project\_predicate\_row\_filter** | String  | Default is empty. This parameter allows to filter data as it is consumed by the project. Please consult the ['Row Filtering' section](symetryml-projects-rest-api.md#about-data-filtering) for more details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **sml\_project\_rolling\_window\_size** | Integer | Size of the rolling window. When specified, the project will only retain statistics from the most recent N rows of data that were learned. This is useful for scenarios where you want the model to adapt to recent data patterns.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **sml\_project\_rolling\_window\_use\_embedded** | boolean | Default is **false**. When set to **true**, creates an embedded SymetryML project inside the main project to handle the rolling window. The embedded project can be referenced in the [Drift Detection API](symetryml-projects-rest-api.md#drift-detection) using the special value `rolling_window_embedded_project` as the analysis project name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |

The following table list mandatory parameters for sequence, partitioned and multi-gpus projects:

| Project Type    | Mandatory Parameters | Description                                                    |
| --------------- | -------------------- | -------------------------------------------------------------- |
| **Sequence**    | sml\_project\_order  | specify the sequence order (ngram)                             |
| **Partitioned** | partitionSplit       | specify which attribute / column to use to split the partition |
| **Multi GPU**   | mgpu\_num            | Number of GPUs to use                                          |

### Create Project Query Parameters

| Parameter           | Required/Optional | Description                                                                                                          |
| ------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| **pid**             | Required          | Name of the new SymetryML project.                                                                                   |
| **Type**            | Optional          | The type of the project. Please see [Project Types](symetryml-projects-rest-api.md#project-types) table for details. |
| **persist**         | Optional          | <p>Whether to persist or not a project. Valid values are</p><p>* true</p><p>* false</p>                              |
| **enableHistogram** | Optional          | Enable histogram for continuous and binary attributes in this project.                                               |

### Project Types

| Project Type  | Descriptions                                                                                                     |
| ------------- | ---------------------------------------------------------------------------------------------------------------- |
| **cpu**       | CPU based project, this is the default value                                                                     |
| **gpu**       | GPU based project                                                                                                |
| **sequence**  | Sequence project, see [About Sequence Projects](symetryml-projects-rest-api.md#about-sequence-projects)          |
| **partition** | Partitioned project, see [About Partitioned Projects](symetryml-projects-rest-api.md#about-partitioned-projects) |
| **mgpuf**     | Multi-GPU project using 32 bits float                                                                            |
| **mgpud**     | Multi-GPU project using 64 bits float - aka double                                                               |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                      |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **201**          | CREATED             | Success. `{"statusCode":"CREATED","statusString":" + SYMETRYML Created with id:r1","values":{}}`                                                 |
| **409**          | CONFLICT            | SymetryML project already exists. `{"statusCode":"CONFLICT","statusString":" + Customer [c1] already have SYMETRYML with id[r1], ","values":{}}` |

### HTTP Response Entity

None

### Sample Request/Response CPU/GPU Projects

```
POST url="http://charm:8080/symetry/rest/c1/projects?pid=testIris

{"statusCode":"CREATED","statusString":"Project Created with id:testIris","values":{}}
```

### Sample Request/Response Partitioned Projects

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=irisMeta&type=partition&persist=true

Request Body:
{"partitionSplit":"class"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:irisMeta","values":{}}
```

### Sample Request/Response Sequence Projects

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=activityMC&type=sequence

Request Body:
{"sml_project_order":"1"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:activityMC","values":{}}
```

### Sample Request/Response Hash-trick Projects

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=CRITEO&type=cpu

Request Body:
{"sml_project_hashtrick_binsize":"1024"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:CRITEO","values":{}}
```

### Sample Request/Response Rolling Window Projects

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=sensorData&type=cpu

Request Body:
{"sml_project_rolling_window_size":"10000","sml_project_rolling_window_use_embedded":"true"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:sensorData","values":{}}
```

## Saving a SymetryML Project

This REST endpoint save a Project into the underlying persistence database - e.g. Redis. This is needed for project that do not use the `autosave` functionality - see [Create New Symetry Project](symetryml-projects-rest-api.md#create-new-symetryml-project-body) for details.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/save
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

## Freezing a SymetryML Project

This REST endpoint _freeze_ a Symetry Project, that is it block it from being able to be updated. Pushing data to a frozen project will raise an Exception and the data will not be processed.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/freeze
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

## Unfreezing a SymetryML Project

This REST endpoint does the reverse of[ Freeze Project](symetryml-projects-rest-api.md#freezing-a-symetryml-project). If the project was not frozen it does nothing.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/unfreeze
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

## Rename a SymetryML project

This API function call renames a SymetryML project.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/rename?newName={newname}
```

### Query Parameters

| Parameter  | Required/Optional | Description                         |
| ---------- | ----------------- | ----------------------------------- |
| **rename** | Required          | New name for the SymetryML project. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                               |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **201**          | CREATED             | Success. `{"statusCode":"CREATED","statusString": + "SYMETRYML Created with id:r1","values":{}}`                                                                          |
| **409**          | CONFLICT            | A SymetryML project by the specified new name already exists. `{"statusCode":"CONFLICT","statusString":"Customer [c1] already have SYMETRYML with id[r1], ","values":{}}` |

### HTTP Response Entity

None

## Assign an Encoder to a SymetryML Project

This API function call assigns an encoder to a SymetryML project.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/encoderAssign?encoderName={encname}
```

### Query Parameters

| Parameter       | Required/Optional | Description                                     |
| --------------- | ----------------- | ----------------------------------------------- |
| **encodername** | Required          | Encoder name to use for this SymetryML project. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

None

## Delete a SymetryML project

This API function call deletes a SymetryML project from your repository.

### URL

```
DELETE /symetry/{cid}/projects/{pid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                               |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success. `{"statusCode":"OK","statusString":"deleted Project with id[r1] from Customer[c1] store","values":{}}`                                           |
| **400**          | BAD REQUEST         | User does not have the SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Customer[c1] does not have Project with id[r1], ","values":{}}` |

### HTTP Response Entity

None

## List SymetryML projects

This API function call retrieves a list of SymetryML IDs that were created by a given user. The response contains a string list containing the names of all the projects that belong to the user who made the request. The format of the response is described in section [Appendix A](appendix-a-json-data-structure-schema.md#stringlist).

### URL

```
GET /symetry/rest/{cid}/projects/list
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Example                                                                                             |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | `{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["r1","r11","r2","r3"]}}}` |

### Sample JSON Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/projects/list

Response:
{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["irisTest","anotherProject"]}}}
```

## SymetryML Project Unload

In order to save memory on the server, it’s possible to unload a project from memory. Note that if you do that with a project that is not persisted then the project cannot be restored.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/unload
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## SymetryML Project Merge

It’s possible to merge 2 SymetryML projects together using the following rest endpoint.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/merge?otherProjectName={otherProject}
```

### Query Parameters

| Parameter        | Required/Optional | Description                                                                                                                                                                                                                                 |
| ---------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **otherProject** | Required          | Name of the other SymetryML project to use for the merging. At the end of this operation `otherProject` will be merge into `pid`. That is `otherProject` will stay the same while `pid` will be updated with the content of `otherProject`. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| 200              | OK                  | Success.    |

## SymetryML Project Information About One Project

This REST API function call retrieves information about a single SymetryML project. The following information about a particular SymetryML project is returned as part of the [ProjectInfo](appendix-a-json-data-structure-schema.md#projectinfo) JSON response.

| Field                    | Description                                                                                                                                                                                                          |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **modelsList**           | List of models                                                                                                                                                                                                       |
| **modelTypeList**        | List of model types                                                                                                                                                                                                  |
| **dsList**               | List of data source names                                                                                                                                                                                            |
| **encoderName**          | Encoder name if the SymetryML project use an encoder                                                                                                                                                                 |
| **attributeNames**       | List of attributes names                                                                                                                                                                                             |
| **attributeIndexes**     | List of attributes index                                                                                                                                                                                             |
| **attributeTypes**       | <p>List of attributes type. For example:</p><p>- ([C] = continuous</p><p>- [B] = binary</p><p>- [S] = string</p><p>- [L] = list</p><p>- [X] = ignore</p>                                                             |
| **pid**                  | Name of this object                                                                                                                                                                                                  |
| **hash**                 | Internal use only                                                                                                                                                                                                    |
| **isDirty**              | Internal use only. This field is deprecated.                                                                                                                                                                         |
| **creationDate**         | Date this project was created.                                                                                                                                                                                       |
| **lastModificationDate** | Last time data was added / learned on this project.                                                                                                                                                                  |
| **modelAssessment**      | Internal use. List of assessments from SymetryML Web application                                                                                                                                                     |
| **modelPredictions**     | Internal use. List of predictions from SymetryML Web application                                                                                                                                                     |
| **categorySeparator**    | Internal use.                                                                                                                                                                                                        |
| **loaded**               | Specifies if a project is loaded in memory or not                                                                                                                                                                    |
| **persisted**            | Specifies if a project is persisted or not                                                                                                                                                                           |
| **streams**              | List of streams that belong to that project                                                                                                                                                                          |
| **partitionColumn**      | For Partitioned Projects, this is the column to use to partition the project                                                                                                                                         |
| **autoSave**             | Whether or not this project is autosaved.                                                                                                                                                                            |
| **type**                 | The type of this project                                                                                                                                                                                             |
| **histogramEnabled**     | Whether or not this project has histogram enabled.                                                                                                                                                                   |
| **fusionCellInfoList**   | For [Fusion](federated-learning-api/fusion-projects.md) projects, this contain the information about the fusion cells. Please see [FusionCellInfoList](appendix-a-json-data-structure-schema.md#fusioncellinfolist). |
| **params**               | A map containing various key / values. See Table Below                                                                                                                                                               |

### Project Params

| Params Key                      | Description                                                                                                           |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **fusion\_fetch\_error**        | Error string from the last time a fusion project fetched data from its cells.                                         |
| **sml\_project\_is\_freeze**    | Describe if this project is in the frozen state, that it is not possible for it to process new incoming data.         |
| **automl\_project\_is\_automl** | Specify that this project use AutoML                                                                                  |
| **automl\_setup\_done**         | When using AutoML, this boolean key indicates that the auto ml 'warm up' is complete.                                 |
| **sml\_project\_power\_column** | Needed for power transformation model. Consult [Model API](modeling-api.md#about-power-regression-model) for details. |
| **sml\_project\_power\_min**    | Needed for power transformation model. Consult [Model API](modeling-api.md#about-power-regression-model) for details. |
| **sml\_project\_power\_max**    | Needed for power transformation model. Consult [Model API](modeling-api.md#about-power-regression-model) for details. |
| **sml\_project\_power\_steps**  | Needed for power transformation model. Consult [Model API](modeling-api.md#about-power-regression-model) for details. |
| **fed\_pulsing**                | If a Federated Project is pulsing or not                                                                              |
| **fed\_has\_error**             | If a Federated Project has error or not                                                                               |
| **fed\_is\_admin**              | If a Federated Project is the admin of the federation it is a member.                                                 |

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/info
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

### HTTP Response Entity

| HTTP Response Entity                                                    | Description            |
| ----------------------------------------------------------------------- | ---------------------- |
| [**ProjectInfo**](appendix-a-json-data-structure-schema.md#projectinfo) | See the example below. |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/projects/irisTest/info"

Response:
{"statusCode":"OK","statusString":"OK","values":{"smlInfo":{"modelsList":["ldaReduced","svmModel"],"modelTypeList":["lda","lsvm"],"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],"pid":"irisTest","attributeStates":["u","u","u","u","u","u","u","u","u","u","u","u","u","u","u"],"hash":-1,"isDirty":true}}}
```

## SymetryML Project Information About All Projects

This REST API function call returns information about all SymetryML projects that were created previously by a given user. To conserve memory usage, minimal information about each SymetryML project loads into server memory. If more information is required, it is loaded "lazily and transparently" by the server. For more information about the response format, see the [ProjectInfoList](appendix-a-json-data-structure-schema.md#projectinfolist).

This API function is asynchronous. If it succeeds, it returns a 202 response, along with a Location header that specifies the job URL. For more information about asynchronous REST calls, see the section "Asynchronous Learning."

### URL

```
GET /symetry/rest/{cid}/projects/info
```

### Query Parameters

| Parameter | Required / Optional | Description                                                                                                                                               |
| --------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **async** | Optional            | If set to true then the ProjectInfo request will be done asynchronously and the result will be fetched using the [Job API.](symetryml-job-information.md) |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                               |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED            | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. For example: `{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}` |

After a Restore job completes, it returns the following information:

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                                                             |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Response entity is a [ProjectInfoList](appendix-a-json-data-structure-schema.md#projectinfolist) JSON data structure. For each SymetryML project ID restored, the entity contains its ID as well as the ID of models that belong to it. |
| **202**          | ACCEPTED            | Job is not finished. Includes a [JobInfo](appendix-a-json-data-structure-schema.md#jobinfo) entity. For more information, see [SymetryML Job](symetryml-job-information.md).                                                            |

### HTTP Response Entity

None

### Sample Request/Response

```
Request
GET url="http://charm:8080/symetry/rest/c1/projects/info"

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{"smlInfoList":{"values":[{"modelsList":[],"modelTypeList":[],"attributeNames":[],"attributeIndexes":[],"attributeTypes":[],"pid":"anotherProject","attributeStates":[],"hash":-1,"isDirty":true},{"modelsList":["ldaReduced","svmModel"],"modelTypeList":["lda","lsvm"],"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],"pid":"irisTest","attributeStates":["u","u","u","u","u","u","u","u","u","u","u","u","u","u","u"],"hash":-1,"isDirty":true}]},"ac":"z3SMHWgE8cqIHJUtBDV8Fg\u003d\u003d","Options":{"gpuEnabled":0}}
```

## Exporting and Importing SymetryML Project Rest API

Export / Import functionality enables the user to export projects and then re-import them. Projects are encoded into a Base 64 string so that they can easily be transmitted using various channels of communication.

### Export a Project

Export a model into a Base 64 string.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/export
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

| HTTP Response Entity | Example                                 |
| -------------------- | --------------------------------------- |
| **String**           | A Base64 representation of the project. |

### Import a Project

Import a model into a project.

#### URL

```
POST /symetry/rest/{cid}/projects/{pid}/import?persist= [body=string]
```

| Parameter   | Required/Optional | Description                            |
| ----------- | ----------------- | -------------------------------------- |
| **persist** | Optional          | Whether or not to persist that project |

### Request Body

The body is a Base 64 string from a project that was exported first with the \[Project Export] functionality.

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Real Time Streaming of Data into SymetryML Projects

SymetryML projects support streaming data. The streaming capabilities are encompassed in 2 major functionalities:

1. Using the [Learn](symetryml-projects-rest-api.md#learn-new-data-incremental-update) or [Forget](symetryml-projects-rest-api.md#forgetting-data-decremental-update) by providing a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) as the request body.
2. Using the [Stream API](stream-data-source-api.md) to create stream data source that automatically push streaming data into your SymetryML project. Use this API to manage connections to streaming server solution such as Apache Kafka.

The following sub-sections will describe the _learn_ and _forget_ streaming API.

### Asynchronous Learning

Both the learn and forget methods can be invoked synchronously or asynchronously. The default method is synchronous. To run the task asynchronously, add async=true as a query parameter to your REST invocation. When invoked, the response is `202 ACCEPTED` and you can use the Job Status API to determine when a learning job is finished. For more information, see [Data Source API](data-source-api.md#data-source-encryption).

Usually, there is no need to run learn and forget tasks asynchronously unless your data has a large number of attributes that, depending on your hardware, can take more or less time to be executed. As a rule of thumb, data with fewer than 100 attributes can be learned and forgotten safely using synchronous execution. However, your experiences might be different. Therefore, if your data has a large number of attributes, we recommend that you perform testing to determine whether the asynchronous or synchronous method will work better for you.

> **Note**
>
> Addition of new attributes is handled by the incremental update automatically, as described in the next section.

### Learn New Data (Incremental Update)

This API function call learns new data. The body of the request should contain a DataFrame JSON data structure. For information about this JSON data structure, see the [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json). For information about the async parameter, see [Asynchronous Learning](symetryml-projects-rest-api.md#asynchronous-learning).

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/learn [?async=true]* [body = DataFrame]
```

### Query Parameters

| Parameter | Required/Optional | Description                                                                                                                                 |
| --------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **async** | Optional          | Performs the operation asynchronously. We recommend using true for this parameter if your data is wider than 100 attributes. Default: false |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                         |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success. `{"statusCode":"OK","statusString":"OK","values":{}}`                                                                                      |
| **202**          | Accepted            | If async == true, the server accepted the request.                                                                                                  |
| **400**          | BAD REQUEST         | SymetryML project cannot be found. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/learn?async=false"

Body:
{"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"data":[["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"]],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{}}
```

### Forgetting Data (Decremental Update)

This API function call allows users to forget data. The body of the request should contain a DataFrame JSON data structure. For information about this JSON data structure, see [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json). For information about the async parameter, see [Asynchronous Learning](symetryml-projects-rest-api.md#asynchronous-learning).

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/forget [?async=true]* [body=DataFrame]
```

### Query Parameters

| Parameter | Required/Optional | Description                                                                                                                                 |
| --------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **async** | Optional          | Performs the operation asynchronously. We recommend using true for this parameter if your data is wider than 100 attributes. Default: false |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                            |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **200**          | OK                  | Success. `{"statusCode":"OK","statusString":"OK"," + values":{}}`                                                                                      |
| **400**          | BAD REQUEST         | If SymetryML project cannot be found: `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

None

### Sample Request/Response

See the sample in the previous [section](symetryml-projects-rest-api.md#sample-request-response-3).

## Data Drift Detection

This REST endpoint detects data drift between a baseline (preference) project and an analysis project. Data drift detection is useful for monitoring model performance degradation over time by comparing the statistical properties of production data against training or validation data.

### URL

```
POST /symetry/rest/{cid}/projects/dataDrift [body=MLContext]
```

### Drift Metrics

The following drift metrics are supported:

| Metric                       | Description                                      |
| ---------------------------- | ------------------------------------------------ |
| **pca_reconstruction_error** | PCA reconstruction error based drift detection   |
| **global**                   | Global drift score across all attributes         |
| **marginal_stat**            | Marginal drift using statistical methods         |
| **marginal_non_stat**        | Marginal drift using non-statistical methods     |

### Request Body

The request body is an [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) containing the following parameters in `extraParameters`:

| Parameter                      | Required / Optional | Description                                                                                                                                     |
| ------------------------------ | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **driftPreferenceProjectName** | Required            | Name of the baseline/reference project to compare against                                                                                       |
| **driftAnalysisProjectName**   | Required            | Name of the analysis project, or `"rolling_window_embedded_project"` to use the embedded rolling window project from the preference project    |
| **driftMetric**                | Required            | One of the drift metrics listed above                                                                                                           |
| **inputAttributeNames**        | Optional            | List of specific attribute names to analyze. If not provided, all attributes are analyzed                                                       |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                    |
| **400**          | BAD REQUEST         | Unknown project or invalid parameters. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                            | Description                                    |
| --------------------------------------------------------------- | ---------------------------------------------- |
| [**KSVDMap**](appendix-a-json-data-structure-schema.md#ksvdmap) | Contains drift scores for the analyzed data.   |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/dataDrift"

Body:
{"inputAttributeNames":["sepal_length","sepal_width","petal_length"],"extraParameters":{"driftPreferenceProjectName":"irisBaseline","driftAnalysisProjectName":"irisProduction","driftMetric":"global"}}

Response:
{"statusCode":"OK","statusString":"OK","values":{"KSVDMap":{"values":[{"driftScore":0.0523,"sepal_length_drift":0.0412,"sepal_width_drift":0.0634,"petal_length_drift":0.0523}]}}}
```

### Using with Rolling Window Projects

For projects configured with rolling windows, you can compare the main project against its embedded rolling window project by specifying `"rolling_window_embedded_project"` as the `driftAnalysisProjectName`. This allows monitoring drift between historical data and recent data within the same project.

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/dataDrift"

Body:
{"inputAttributeNames":[],"extraParameters":{"driftPreferenceProjectName":"irisProject","driftAnalysisProjectName":"rolling_window_embedded_project","driftMetric":"marginal_stat"}}
```
