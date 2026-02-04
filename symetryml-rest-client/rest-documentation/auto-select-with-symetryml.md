# Auto Select with SymetryML

## Auto Select Introduction

The auto-select build on top of the [Select Model](select-model-with-symetryml.md) functionality and allows to automate finding the best model for a given task (binary classification or regression model). This functionality will try various combinations of input attributes on various types of models and select the best one automatically. Auto Select perform the following:

1. Using a heuristic - see [Select Heuristic](select-model-with-symetryml.md#select-heuristic) - it will try various combination of the input attributes to build a model
   * When using a regression Task, there is also another parameter that controls the grid search size. Please see the [Select Grid](select-model-with-symetryml.md#selector-grid) table for details
2. It uses a provided out-of-sample datasets to compute a score for the model. Example of scoring are AUC or RMSE.
3. It repeats step (1) and (2) to build many models - possibly thousands - and then select the best model.

Depending on how many attributes your project has it can take anywhere a few seconds to several hours. Be sure to understand the [Select Heuristic](select-model-with-symetryml.md#select-heuristic) section as well as the [Select Grid](select-model-with-symetryml.md#selector-grid).

## Auto Select Rest API

Allows to invoke the _auto select_ functionality by specifying an external data source id as the out of sample data to use for model assessment.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/autoSelect/{dsid} [body=MLContext]
```

### Query Parameters

| Parameter   | Required / Optional | Description                                                                         |
| ----------- | ------------------- | ----------------------------------------------------------------------------------- |
| **modelid** | Required            | ID to assign to the new model.                                                      |
| **task**    | Required            | Task to perform `binary_classification` or `regression` or `multiclass_classifier`. |

### MLContext Build Parameters

| Parameter                          | Required / Optional | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------- | ------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rnd\_seed**                      | Optional            | Integer | Set the seed of the randomizer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **selector\_type**                 | Optional            | String  | Default is **selector\_type\_fw\_bw**. Please see [Selector Heuristic](select-model-with-symetryml.md#select-heuristic) and [Selector Types](select-model-with-symetryml.md#selector-types) sections for details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **autoselect\_grid\_type**         | Optional            | String  | Default is **autoselect\_grid\_type\_tiny**. Please see [Select Grid](select-model-with-symetryml.md#selector-grid) for details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **autoselect\_thread\_pool\_size** | Optional            | Integer | Default is 8.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **sml\_model\_assessment\_type**   | Optional            | String  | <p><strong>Defaults are</strong></p><ul><li><code>auc</code> for binary classifier</li><li><code>rmse</code> for regression</li><li><code>mf1_w</code> for multi-class classifier, that is multi class F1 weighted scoring.</li></ul><p><strong>Possible value are:</strong> </p><ul><li><p>classifier tasks</p><ul><li><code>auc</code></li><li><code>recall</code></li><li><code>precision</code></li><li><code>mcc</code> Matthews correlation coefficient</li></ul></li><li><p>regression tasks</p><ul><li><code>rmse</code></li><li><code>r2</code></li><li><code>rmslogp1e</code> Root mean square of the sum of the log of the error</li></ul></li><li><p>multi-class classifier tasks:</p><ul><li><code>mf1_w</code></li><li><code>mkappa</code>, multi-class Kappa</li></ul></li></ul> |

### HTTP Responses

|HTTP Status Code |HTTP Status Message |Description |202 | OK | Job accepted. |400 | BAD REQUEST | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}`

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/autoSelect/Iris_rtlm.csv?task=binary_classifier&modelid=autoSelectModel1

Body:
{"targets":[],"inputAttributes":[],"inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2"],"targetAttributeNames":["Iris_virginica"],"extraParameters":{"rnd_seed":"1"}}
```

### Sample Request Response Regression

This example uses:

* regression task: `?task=regression`
* specify the heuristic: `"selector_type":"selector_type_fw_bw"`
* specify the grid search: `"autoselect_grid_type":"autoselect_grid_type_small"`

```
Request:
POST url="/symetry/rest/c1/projects/p1/autoSelect/Iris_rtlm.csv?task=binary_classifier&modelid=autoSelectModel1

Body:
{"targets":["13"],"inputAttributes":["44","88","45","89","46","47","48","49","90","91","50","51","52","53","10","54","11","55","12","56","57","14","58","15","59","16","17","18","19","0","1","2","3","4","5","6","7","8","9","60","61","62","63","20","64","21","65","22","66","23","67","24","68","25","69","26","27","28","29","70","71","72","73","30","74","31","75","32","76","33","77","34","78","35","79","36","37","38","39","80","81","82","83","40","84","41","85","42","86","43","87"],"inputAttributeNames":[],"targetAttributeNames":[],"extraParameters":{"rnd_seed":"1","selector_type":"selector_type_fw_bw","autoselect_grid_type":"autoselect_grid_type_small"}}
```

## Auto Select DataFrame Rest API

Allows to invoke the _auto select_ model functionality by using a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) passed in the request body.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/autoSelect [body=Map{"dataframe"=DataFrame, "mlcontext"=MLContext}]
```

### Query Parameters

| Parameter   | Required / Optional | Description                                                                        |
| ----------- | ------------------- | ---------------------------------------------------------------------------------- |
| **modelid** | Required            | ID to assign to the new model.                                                     |
| **task**    | Required            | Task to perform `binary_classification` or `regression` or `multiclass_classifier` |

### MLContext Build Parameters

Same as [Auto Select Rest](automl-with-symetryml.md#auto-ml-rest-api) API. Please consult the [MLContext Build Parameters section](auto-select-with-symetryml.md#mlcontext-build-parameters) for details.

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | OK                  | Job accepted.                                                                                                                               |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/autoSelect?task=binary_classifier&modelid=autoSelectModel1

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

This example uses:

* regression task: `?task=regression`
* specify the heuristic: `"selector_type":"selector_type_fw_bw"`
* specify the grid search: `"autoselect_grid_type":"autoselect_grid_type_small"`

```
TBD
```
