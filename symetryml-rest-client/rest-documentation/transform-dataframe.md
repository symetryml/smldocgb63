# Transform Dataframe

This rest endpoint returns a transformed [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) if the project is AutoML enabled or the project has time series transformation enabled. The transformation applied are dictated by the AutoML/time series expansion configuration for the project, please see [AutoML Control Parameters](automl-with-symetryml.md#automl-control-parameters) section for details. For regular projects the transform endpoint will return the original [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json).

## URL

```
POST /symetry/rest/{cid}/projects/{pid}/transform [body=DataFrame]
```

## HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Transformation OK                                                                                                                           |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

## HTTP Response Entity

The response is a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json), with the corresponding AutoML transformations as specified during project creation.

## Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/transform"

Body:
{
    "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
    "attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],
    "data":[
        ["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],
        ["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],
        ["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"],
        (...)
        ["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]
    ]
}

Response:
{
    "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_length_*_sepal_length","Iris_setosa"],
    "attributeTypes":["C","C","C","C","B"],
    "data":[
        ["4.3","3","1.1","0.1","0.11", "1"],
        ["4.8","3","1.4","0.1","0.114","1"],
        ["4.9","3.1","1.5","0.1","0.15","1"],
        (...)
        ["7.2","3.6","6.1","2.5","15.25","0"]
    ]
}
```
