# Exporting and Importing Model

Export / Import functionality enables the user to export models and then re-import them into another project. Models are encoded into a Base 64 string so that they can easily be transmitted using various channels of communication.

## Exporting and Importing Model Rest API

### Export a Model

Export a model into a Base 64 string.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/model/export?modelid
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

| HTTP Response Entity | Example                               |
| -------------------- | ------------------------------------- |
| **String**           | A Base64 representation of the model. |

#### Sample Request/Response Fed Create

```
GET url="http://charm:8080/symetry/rest/c1/projects/p1/model/export?modelid=theModelName"

Response:
Base64 String
```

### Import a Model

Import a model into a project.

#### URL

```
POST /symetry/rest/{cid}/projects/{pid}/model/import?modelid= [body=string]
```

### Request Body

The body is a Base 64 string from a model that was exported first with the [Model Export](exporting-and-importing-model.md#export-a-model) functionality.

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### Sample Request/Response Fed Create

```
POST url="http://charm:8080/symetry/rest/c1/projects/p1/model/export?modelid=theModelName"

BODY:
Base64 string
```
