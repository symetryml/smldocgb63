# Select Model with SymetryML

## Select Model introduction

SymetryML **Select Model** is a feature selection functionality. It allows to automatically select the best features for a given model algorithm and it leverages SymetryML unique capabilities to build different predictive model quickly. The functionality builds various model each with different input attributes using a predefined heuristic. It then computes a score for them using out of sample data and will retain the best one. The following table describes the available heuristics:

### Select Heuristic

| Select heuristic             |                                                                                                                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                         | Description                                                                                                                                                                                                                           |
| **Forward Backward**         | A heuristic that does the following: 1. Iteratively add as many features as possible while keeping the best model 2. Iteratively remove as many feature as possible while keeping the best model 3. repeat a specific number of time. |
| **Brute Force**              | Brute force will try all possible combinations of the input attributes. It should not be used if you have more than 17-18 attributes.                                                                                                 |
| **Max Number of Iterations** | Randomly create a model by trying a specific number of random number of permutations of the features.                                                                                                                                 |
| **Max. Number of Seconds**   | Randomly create a model by trying a random number of permutations of the features for a maximum number of seconds.                                                                                                                    |
| **Simple**                   | The simple heuristic starts with one feature and then incrementally adds one additional feature until it tries all the features. It then keeps track of the best model.                                                               |

### Selector Types

| Parameter                     | Description                                                                                                                                                                                                                 |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **selector\_type\_fw\_bw**    | Forward / Backward heuristic. Number of iteration is by default 5. It can be controlled with the `selector_max_iterations` parameters.                                                                                      |
| **selector\_type\_simple**    | Simple heuristic                                                                                                                                                                                                            |
| **selector\_type\_brute**     | Brute force selector.                                                                                                                                                                                                       |
| **selector\_type\_iteration** | A Selector that will either try a specific number of random combination or will try for a specific number of seconds. `selector_max_iterations` or `selector_max_seconds` must also be specified with this type of selector |

### Selector Grid

Elastic Net model has 2 hyper parameters that can be optimized _eta_ and _lambda_. The auto-select algorithm will try various combinations of these parameters using a grid search. The size of this grid can be controlled via the `autoselect_grid_type` extra parameter in the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) request body. Please see this [section](auto-select-with-symetryml.md#sample-request-response-regression-1) for such an example.

| Parameter                          | Description                                                                                                         |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **autoselect\_grid\_type\_tiny**   | eta \[0, 0.5, 1.0] x lambda \[1e-3, 1e-2, 0.1]                                                                      |
| **autoselect\_grid\_type\_small**  | eta \[0, 0.5, 1.0] x lambda \[1e-3, 1e-2, 0.1, 1]                                                                   |
| **autoselect\_grid\_type\_normal** | eta \[0, 0.3333, 0.6666, 1.0] x lambda \[1e-4, 1e-3, 1e-2, 0.1, 1, 10]                                              |
| **autoselect\_grid\_type\_large**  | eta \[0, 0.2, 0.4, 0.6, 0.8, 1.0] x lambda \[1e-9, 1e-8, 1e-7, 1e-6, 1e-5, 1e-4, 1e-3, 1e-2, 0.1, 1, 10, 100, 1000] |

## Select Model Rest API

Allows to invoke the _select model_ functionality by specifying an external data source id as the out of sample data to use for model assessment.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/selectModel/{dsid} [body=MLContext]
```

### Query Parameters

| Parameter   | Required / Optional | Description                    |
| ----------- | ------------------- | ------------------------------ |
| **modelid** | Required            | ID to assign to the new model. |
| **algo**    | Required            | Algorithm to fit               |

### MLContext Build Parameters

| Parameter                  | Required / Optional | Type    | Description                                                                                                                                                                                                       |
| -------------------------- | ------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rnd\_seed**              | Optional            | Integer | Set the seed of the randomizer                                                                                                                                                                                    |
| **selector\_type**         | Optional            | String  | Default is **selector\_type\_fw\_bw**. Please see [Selector Heuristic](select-model-with-symetryml.md#select-heuristic) and [Selector Types](select-model-with-symetryml.md#selector-types) sections for details. |
| **autoselect\_grid\_type** | Optional            | String  | Default is **autoselect\_grid\_type\_tiny**. Please see [Selector Grid Table](select-model-with-symetryml.md#selector-grid) for details.                                                                          |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| 202              | OK                  | Job accepted.                                                                                                                               |
| 400              | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/Iris_rtlm.csv?algo=lda&modelid=lda1

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
    "targetAttributeNames":["Iris_virginica"],
    "extraParameters":{"rnd_seed":"1"}
}
```

### Sample Request Response Regression

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/Iris_rtlm.csv?algo=mlr&modelid=mlr1

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
    "targetAttributeNames":["sepal_length"],
    "extraParameters":{"rnd_seed":"1"}}

}
```

## Select Model Dataframe Rest API

Allows to invoke the _select model_ functionality by using a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) passed in the request body as the out of sample data to be used for models assessment.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/selectModel [body=Map{"dataframe"=DataFrame, "mlcontext"=MLContext}]
```

### Query Parameters

| Parameter   | Required / Optional | Description                    |
| ----------- | ------------------- | ------------------------------ |
| **modelid** | Required            | ID to assign to the new model. |
| **algo**    | Required            | Algorithm to fit               |

### MLContext Build Parameters

| Parameter                  | Required / Optional | Type    | Description                                                                                                                                                                                                       |
| -------------------------- | ------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rnd\_seed**              | Optional            | Integer | Set the seed of the randomizer                                                                                                                                                                                    |
| **selector\_type**         | Optional            | String  | Default is **selector\_type\_fw\_bw**. Please see [Selector Heuristic](select-model-with-symetryml.md#select-heuristic) and [Selector Types](select-model-with-symetryml.md#selector-types) sections for details. |
| **autoselect\_grid\_type** | Optional            | String  | Default is **autoselect\_grid\_type\_tiny**. Please see [Selector Grid Table](select-model-with-symetryml.md#selector-grid) for details.                                                                          |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | OK                  | Job accepted.                                                                                                                               |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel?algo=lda&modelid=lda1

Body:
{
    "mlcontext" : {
        "targets":[],
        "inputAttributes":[],
        "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "targetAttributeNames":["Iris_virginica"],
        "extraParameters":{"rnd_seed":"1"}}
    "dataframe" : {
        "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]
        "data":[
            ["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"]
            (...)
            ["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]
        ],

    }

}
```

### Sample Request Response Regression

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel?algo=mlr&modelid=mlr1

Body:
{
    "mlcontext" : {
        "targets":[],
        "inputAttributes":[],
        "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "targetAttributeNames":["sepal_length"],
        "extraParameters":{"rnd_seed":"1"}}
    "dataframe" : {
        "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]
        "data":[
            ["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"]
            (...)
            ["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]
        ],

    }
}
```
