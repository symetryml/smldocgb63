# Tasks API

The task suite of Rest API methods allows to create, start, stop and list SymetryML Kafka stream applications. These applications are meant to automatically perform prediction on a Kafka Topic and then save the prediction results in another Kafka topic.

There is a maximum number of task that can be run at the same time. This number is controlled by the `sml.task.max` SymetryML control parameter. Please consult the [Installation Guide](../../guides/installation-guide/#about-the-symetryml-configuration) for more details on how to set this parameters. The default value for this `sml.task.max` parameter is 4. Please consult the [SML Kafka Stream Application Documentation](https://github.com/symetryml/smldocgb/blob/master/kafka-stream-app.md) for details.

Some additional parameters can be set inside the [TaskInfo](appendix-a-json-data-structure-schema.md#taskinfo-json) JSON data structure with the `params` map field:

| Parameter           | Information        |
| ------------------- | ------------------ |
| `sml.task.heap.min` | default is `1024m` |
| `sml.task.heap.max` | default is `2048m` |

## Task Create

This API function creates a Task. Please see [Data Source Encryption](data-source-api.md#data-source-encryption) for information on how to encrypt [TaskInfo](appendix-a-json-data-structure-schema.md#taskinfo-json) data structures.

### URL

```
POST /symetry/rest/{cid}/tasks/create [Body=TaskInfo (encrypted)]
```

| Parameter | Required / Optional | Description                                                                          |
| --------- | ------------------- | ------------------------------------------------------------------------------------ |
| **force** | Optional            | if true then will override any pre-existing Task with same id. Default is **false**. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                        |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------- |
| **202**          | CREATED             | Success.                                                                           |
| **409**          | CONFLICT            | A task with the specified name already exists and the `force` parameter was false. |

## Task Start

This rest endpoint start a task.

### URL

```
GET /symetry/rest/{cid}/tasks/{tid}/start
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Task Start

This rest endpoint stop a task.

### URL

```
GET /symetry/rest/{cid}/tasks/{tid}/stop
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Task List

This rest endpoint return a list of tasks that belong to a given project for a given user.

### URL

```
GET /{cid}/tasks
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Description          |
| --------------------------------------------------------------------- | -------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | A list of task name. |

## Delete a Task

Delete a task If the task is running it will first be stopped.

### URL

```
DELETE /symetry/rest/{cid}/tasks/{tid}
```

### Canonical URL Parameters

| Parameter | Description |
| --------- | ----------- |
| **cid**   | Customer ID |
| **tid**   | Task ID     |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

## Task Status

This rest endpoint return information about a task.

### URL

```
GET /symetry/rest/{cid}/tasks/{tid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                            | Description                          |
| --------------------------------------------------------------- | ------------------------------------ |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | A map with key, value as string pair |

#### Status Parameter returned

| Parameter          | Description                           |
| ------------------ | ------------------------------------- |
| **status**         | Whether or not the task is running.   |
| **startDateTime**  | Time the task was started if running. |
| **processIsAlive** | Is the underlying process alive?      |
| **lastError**      | Last error string.                    |
