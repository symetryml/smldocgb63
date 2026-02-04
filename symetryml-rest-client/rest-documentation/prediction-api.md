# Prediction API

## Dynamic Imputing of Missing Value for Predictions

Real world data often times contains missing values. While these missing values are handled gracefully by some machine learning algorithms, others, require all values to be present in order to generate a prediction. For the latter SymetryML can impute the missing values in one of the following ways:

* Replace the missing value by the average of its column. This is the default behaviour.
* Impute the missing attribute by building a separate model which will attempt to predict the missing attribute based on the the attributes that are available.

The second option is what we refer to as the Dynamic Imputer. The online nature of SymetryML enables these imputation models to be build on the fly as the prediction file is being processed. Enabling this functionality is as simple as specifying **impute=true** in the query parameter of the Predict APIs.

### Sample Request/Response DS Predict

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/dss/predict/testLDA?indsname=dsin&outdsname=dsout&impute=true
Request Body:
{
  "attributeNames":["sepal_length","sepal_width","petal_length","petal_width"],
  "attributeTypes":["C","C","C","C"]
}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/2

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/2

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{"dataframe":{"attributeNames":["resZ_Result","res_Result","normZ_Result","sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"data":[["-0.4559922893809558","1","0.2929126883636288","4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],["-0.5225734165408404","1","0.2965158290613048","

(…)

["-2.6526076600477295","1","0.4232667751179995","6.9","3.1","5.4","2.1","0","1","0","1","0","1","0","1","0","0","1"]]}}}
```

### Sample Request/Response Predict

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/predict/testLDA?impute=true"

Body:
{"attributeNames":["petal_length","sepal_width"],"data":[["1.5","5"]],"attributeTypes":["C","C"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"KSVSMap":{"values":[{"res":"1","normZ":"0.40997859439073814","resZ":"-2.4426003268943006","z0":"-3.840891864408894","z1LTz0":"false"}]}}}
```

## Prediction REST API Canonical URL

```
POST /symetry/rest/{cid}/projects/{pid}/predict/{modelid} [body=DataFrame]
```

## HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                                                              |
| ---------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                    | Success.                                                                                                                                                 |
| **400**          | BAD REQUEST           | Invalid RTM ID. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}`                            |
| **500**          | INTERNAL SERVER ERROR | Invalid model ID. `{"statusCode":"INTERNAL_SERVER_ERROR","statusString":"Generic Exception \u003cError no such model \u003cm3\u003e\u003e","values":{}}` |

## HTTP Response Entity

| HTTP Response Entity                                            | Example                                   |
| --------------------------------------------------------------- | ----------------------------------------- |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | Contain information about the prediction. |

## Sample Request Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/predict/testLDA"

Body:
{"attributeNames":["petal_length","sepal_width"],"data":[["1.5","5"]],"attributeTypes":["C","C"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"KSVSMap":{"values":[{"res":"1","normZ":"0.40997859439073814","resZ":"-2.4426003268943006","z0":"-3.840891864408894","z1LTz0":"false"}]}}}
```

## LearnPredict REST API

Combines learn and predict operations in a single call. Allows learning new data and getting predictions in one request.

### LearnPredict REST API Canonical URL

```
POST /symetry/rest/{cid}/projects/{pid}/learnpredict/{modelid} [body=DataFrame]
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `keepColumns` | String | - | Comma-separated list of columns to keep in response |
| `predictBeforeLearn` | String | `"true"` | If true, predict before learning the data |
| `rebuildModel` | String | `"false"` | If true, rebuild the model after learning |

### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description |
| ---------------- | --------------------- | ----------- |
| **200**          | OK                    | Success.    |
| **400**          | BAD REQUEST           | Invalid project or model ID. |
| **500**          | INTERNAL SERVER ERROR | Server error during learn/predict operation. |

### HTTP Response Entity

| HTTP Response Entity | Description |
| -------------------- | ----------- |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | Contains prediction results. |

### Sample Request Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/learnpredict/testLDA?predictBeforeLearn=true&rebuildModel=false"

Body:
{"attributeNames":["petal_length","sepal_width","petal_width","sepal_length","species"],"data":[["1.5","3.2","0.2","5.1","setosa"]],"attributeTypes":["C","C","C","C","C"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"KSVSMap":{"values":[{"res":"1","normZ":"0.40997859439073814","resZ":"-2.4426003268943006"}]}}}
```

## Model Prediction Map Keys

The prediction service returns a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap). This map is an array of map\<key, value>, with key and value being strings. This is different from KSVDMap, where the values are double. The following table lists the model types and their corresponding KSVDMap key. The following table enumerates the keys returned by each model.

| Model Types                         | Returned Keys                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ECOD**                            | <ul><li><strong>res</strong>:1 or 0 (1 if ecodscore > chosen threshold score)</li><li><strong>ecodscore</strong>: the anomaly score of the ECOD model</li></ul>                                                                                                                                                                                                                                                                                                                          |
| **EVT**                             | <ul><li><strong>evt_is_anomaly</strong>: 1 or 0 (1 if score > threshold)</li><li><strong>evt_threshold</strong>: value of threshold if EVT is one sided</li><li><strong>evt_threshold_upper</strong>: value of the upper threshold if EVT is double sided</li><li><strong>evt_threshold_lower</strong>: value of the lower threshold if EVT is double sided</li></ul>                                                                                                                    |
| **Partial Least Square Regression** | If OneQ is true **q** Else **1 … q** predictions                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **Principal Component Regression**  | If `pcr_type == full` return **res**. Otherwise, If OneQ is true return **q** Else **1 … q**                                                                                                                                                                                                                                                                                                                                                                                             |
| **LDA** & **MLDA**                  | <p>- <strong>res</strong>, 1 or 0<br>- <strong>resZ</strong><br>- <strong>z0</strong><br>- <strong>z1GTz0</strong><br>Examples:<br><strong>res :1</strong><br><strong>resZ :-1.66850</strong><br><strong>z0 :0.240482</strong><br><strong>z1GTz0 :true</strong></p><p>LDA also return <strong>resU</strong>, which is similar to <strong>res</strong> but use a different threshold for when the positive and negative examples are unbalanced in the training data.<br></p>             |
| **LSVM**                            | - **res**, 1 or 0                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **LSVR**                            | - **res**, the regression result                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **MLR**                             | - **res**, the regression result                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **QDA**                             | <p>For each target in the model, a key,value pair is returned. The format of the key is ‘&#x3C;<em><strong>attribute name></strong></em>’. The value is the number computed by the model for that target.<br>Example:<br><strong>Corn :12.0509 *</strong><br><strong>*Sugarbeets :11.4417 *</strong><br><strong>*Clover :11.7757 *</strong><br><strong>*Soybeans :12.0040</strong><br><strong>Cotton :12.0634</strong><br>The best scoring target is also returned in the ‘res’ key.</p> |
| **Bayes**                           | <p><strong>res</strong> = 0 or 1.<br><strong>Prob</strong> = contains the likelihood of the result.</p>                                                                                                                                                                                                                                                                                                                                                                                  |
| **Markov Chains**                   | <p><strong>next</strong> = contains the next state<br><strong>next.p</strong> = contains probability of the next state<br><strong>seq.like</strong> = contains the likelihood of the input sequence</p>                                                                                                                                                                                                                                                                                  |
| **Hidden Markov Model**             | seq = sequence of hidden states                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Random Forest Classifier**        | -**res**, the class id with the highest probability. The probability for each classes is also returned. For a model with 3 classes their ids would be \[0, 1, 2].                                                                                                                                                                                                                                                                                                                        |
| **Kaplan Meier**                    | Prediction is not defined for Kaplan Meier model. Predict should not be called on models of type KM.                                                                                                                                                                                                                                                                                                                                                                                     |
