# Third Party Model Rest API

SymetryML allows you to build _third party_ models. Currently only XGBoost models can be built. These models are different than SymetryML models in the following:

* Third party models do not belong to a particular project. They belong to the user who creates them.
* They are built using 2 data sources:
  * In Sample data source
  * Out of sample data source

## Third Party Model Build

Build a new third party model for a given user.

### URL

```
POST /symetry/rest/{cid}/tmodels/build [body=MLContext]
```

### Query Parameters

| Parameter   | Required / Optional | Description                                                                 |
| ----------- | ------------------- | --------------------------------------------------------------------------- |
| **modelid** | Required            | ID to assign to the new model.                                              |
| **algo**    | Required            | Algorithm to use to build the model. Only `xgb_model` is supported for now. |
| **isdf**    | Required            | In sample data source to be used to build the model.                        |
| **oosdf**   | Required            | Out of sample data source to be used to build the model.                    |

### MLContext Build Parameters

Many parameters can be passed to this methods depending on what type of XGBoost model one wants to build. See [Sample Request](third-party-model-rest-api.md#sample-request-response) for an example. Basically, XGBoost parameters are passed as `extraParameters` inside the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) data structure.

| Parameter        | Required/Optional? | Type  | Description                      |
| ---------------- | ------------------ | ----- | -------------------------------- |
| **missing\_val** | Optional           | Float | XGBoost missing value parameter. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### Sample Request Response

The following is an example on how to set XGBoost parameters with the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) structure when building a xgboost model:

```
Request:
POST url="http://charm:8080/symetry/rest/c1/tmodels/build/modelid=xgbM1&modelType=xgb_model&isds=inputDS&oosds=oosDS

Body:
{"targets":[],"inputAttributes":[],"inputAttributeNames":["sepal_length","sepal_width","petal_length"],"targetAttributeNames":["petal_width"],"extraParameters":{"eta":"0.5","eval_metric":"rmse","max_depth":"4","objective":"reg:linear"}}
```

## Third Party Model Prediction

### URL

```
POST /symetry/rest/{cid}/tmodels/{modelid}/predict [body=DataFrame]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                                                              |
| ---------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                    | Success.                                                                                                                                                 |
| **500**          | INTERNAL SERVER ERROR | Invalid model ID. `{"statusCode":"INTERNAL_SERVER_ERROR","statusString":"Generic Exception \u003cError no such model \u003cm3\u003e\u003e","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                            | Example                                   |
| --------------------------------------------------------------- | ----------------------------------------- |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | Contain information about the prediction. |

### Sample Request Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/tmodels/xgbmodel1/predict"

Body:
{"attributeNames":["petal_length","sepal_width"],"data":[["1.5","5"]],"attributeTypes":["C","C"]}
```

## Third Party Model Information

### URL

```
GET /symetry/rest/{cid}/tmodels/{modelid}/info
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

For information about the ModelInfo JSON data structure see the section [ModelInfo](appendix-a-json-data-structure-schema.md#modelinfo).

| HTTP Response Entity                                                | Description                       |
| ------------------------------------------------------------------- | --------------------------------- |
| [**ModelInfo**](appendix-a-json-data-structure-schema.md#modelinfo) | Contains information about model. |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/tmodels/xgbmodel1/info"

Response:
{"statusCode":"OK","statusString":"OK","values":{"modelInfo":{"modelId":"xgb-model1","modelType":"xgb_model","modelBuilt":1,"buildTime":7026012,"attributeNames":["sepal_length","sepal_width","petal_length"],"targetNames":["petal_width"],"info":{},"creationDate":1605813840224,"useShortDesc":false}}}
```

## Delete a Third Party Model

Delete a particular model

### URL

```
DELETE /symetry/rest/{cid}/tmodels/{modelid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

## List Third Party Model

List all third party models that belong to a user.

### URL

```
GET /symetry/rest/{cid}/tmodels
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Example                                                                                            |
| --------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | See [bellow](third-party-model-rest-api.md#sample-request-response-tmodel-create) for more details |

### Sample Request/Response TModel Create

```
GET url="http://charm:8080/symetry/rest/c1/tmodels"

Response:
{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["xgbmodel1","xgbmodel2"]}}}
```
