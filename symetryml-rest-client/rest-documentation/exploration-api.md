# Exploration API

## About the Exploration API

The Exploration API allows for the request of statistical metrics of SymetryML projects using univariate, bivariate, Hypothesis Testing (ztest, test, ftest, ANOVA) as well as Information Gain analyses. The body of the request contains a [ExploreContext](appendix-a-json-data-structure-schema.md#explorecontext-json) which in turn is a list of [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json), one for each feature for which exploration data is needed.

#### MLContext Parameters for Peer Exploration <a href="#mlcontext-parameters-for-density-estimates" id="mlcontext-parameters-for-density-estimates"></a>

If the project is part of a federation it might be possible to ask that the exploration result is fetched from a given node. This can be done by specifying which peer to use with the following extra parameters inside a  [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json). Please consult the [About Federated Learning : Peer Exploration](about-federated-learning.md#peer-exploration) section for details.

| Key                               |                                         |
| --------------------------------- | --------------------------------------- |
| **fed\_peer\_uuid\_for\_explore** | Peer ID to use for exploration results. |



### URL

```
POST /symetry/rest/{cid}/projects/{pid}/explore?metric={_metric_} [body=ExploreContext]
```

### Query Parameters

| Parameter  | Required / Optional | Description                                                                                                                                       |
| ---------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **metric** | Required            | Metric to use for exploration. For more information, see the section [List of Metrics](exploration-api.md#list-of-metric-query-parameters)        |
| **async**  | Optional            | If set to true then the exploration will be done asynchronously and the result will be fetched using the [Job API.](symetryml-job-information.md) |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                               |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                  |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

The response is a [KSVDMap](appendix-a-json-data-structure-schema.md#ksvdmap) entity. This structure is an array of `map<key,value>`, where keys are string and values are an Institute of Electrical and Electronics Engineers (IEEE) 64-bit double floating-point number. One such map is added to the response for each [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) in the [ExploreContext](appendix-a-json-data-structure-schema.md#explorecontext-json) request body. The keys in each map depend on the metric query parameter. For more information, see the section [ExploreContext](appendix-a-json-data-structure-schema.md#explorecontext-json).

| HTTP Response Entity                                                    | Example                                                                                                                                                                                                       |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**KSVDMap**](appendix-a-json-data-structure-schema.md#ksvdmap)         | `{"statusCode":"OK","statusString":"OK","values":{"KSVDMap":{"values":[{"count":150.0,"mean":5.843333333333334,"variance":0.6811222222222204,"skewness":0.31808651443435426,"stddev":0.8253012917851398}]}}}` |
| [**ProjectInfo**](appendix-a-json-data-structure-schema.md#projectinfo) | A project info entity is also returned to reflect the state of the project that might have changed.                                                                                                           |

## List of "Metric" Query Parameters

Depending on the metric query parameter, different information is returned in the KSVDMap JSON response entity. The KSVDMap JSON entity is an array of map\<key, value>, where key is a string and value is an IEEE 64-bit double floating-point number. The following table enumerates the keys that the response will contain for all valid metric query parameters.

| Metric                      | Description                                                                                                                                                                                                                 | KSVDMap Key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **uni**                     | Returns all univariate metrics for the attribute listed in each MLContext in the ExploreContext. If an MLContext has more than one attribute, only the first one is used.                                                   | <p>- <strong>count</strong> = count of the attribute.</p><p>- <strong>Mean</strong> = mean of the attribute.</p><p>- <strong>variance</strong> = variance of the attribute.</p><p>- <strong>stddev</strong> = standard deviation of the attribute.</p><p>- <strong>skewness</strong> = skewness of the attribute.<br>- <strong>stderr</strong> = the standard deviation divided by the square root of the count for this feature.<br>- <strong>meanMarginErrorC95</strong> = Mean standard error 95% confidence level.</p>                                                                                                                                                                                                                                                                                                               |
| **uni** _(with histogram)_  | For project with histogram enabled, additional information is returned. Please consult [this section ](symetryml-projects-rest-api.md#create-project-query-parameters)to learn how to enable histogram for a project.       | <p>- <strong>min</strong> = minimum value for this feature.</p><p>- <strong>max</strong> = maximum value for this feature.</p><p>- <strong>median</strong> = median value for this feature.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **bi**                      | Returns all bivariate metrics for the two attributes listed in each MLContext in the ExploreContext. Each MLContext should contain at least two attribute indexes. If there are more than two, only the first two are used. | <p>- <strong>covar</strong> = covariance between the two attributes.</p><p>- <strong>linCorr</strong> = linear correlation between the two attributes.</p><p>- <strong>condMean</strong> = conditional mean</p><p>- <strong>condCount</strong> = conditional count</p><p>- <strong>condVariance</strong> = conditional variance</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **ztest**                   | Returns the z-test metrics using all of the MLContext in the ExploreContext request body.                                                                                                                                   | <p>- <strong>zn1</strong> = count between attribute and target 1.</p><p>- <strong>zn2</strong> = count between attribute and target 2.</p><p>- <strong>zm1</strong> = mean between attribute and target 1.</p><p>- <strong>zm2</strong> = mean between attribute and target 2.</p><p>- <strong>zs1</strong> = variance between attribute and target 1.</p><p>- <strong>zs2</strong> = variance between attribute and target 2.</p><p>- <strong>z</strong> = z test value.</p><p>- <strong>zp</strong> = z test value probability.</p>                                                                                                                                                                                                                                                                                                    |
| **ztestp**                  | Returns the z-test-proportion metrics using all of the MLContext in the ExploreContext request body.                                                                                                                        | <p>- <strong>z</strong> = z test value.</p><p>- <strong>zp</strong> = z test value probability.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **ztestmu**                 | Returns the z-test - against a known mean - metrics using all of the MLContext in the ExploreContext request body.                                                                                                          | <p>- <strong>z</strong> = z test value.</p><p>- <strong>zp</strong> = z test value probability.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **ftest**                   | Returns the f-test metrics using all of the MLContext in the ExploreContext request body.                                                                                                                                   | <p>- <strong>fn1</strong> = count between attribute and target 1.</p><p>- <strong>fn2</strong> = count between attribute and target 2.</p><p>- <strong>fm1</strong> = mean between attribute and target 1.</p><p>- <strong>fm2</strong> = mean between attribute and target 2.</p><p>- <strong>fs1</strong> = variance between attribute and target 1.</p><p>- <strong>fs2</strong> = variance between attribute and target 2.</p><p>- <strong>fdf1</strong> = degree of freedom between attribute and target 1.</p><p>- <strong>fdf2</strong> = degree of freedom between attribute and target 2.</p><p>- <strong>f</strong> = F test value.</p><p>- <strong>fp</strong> = F test value probability.</p>                                                                                                                                |
| **ftestmu**                 | Returns the f-test - against a known variance - metrics using all of the MLContext in the ExploreContext request body.                                                                                                      | <p>- <strong>f</strong> = F test value.</p><p>- <strong>fp</strong> = F test value probability.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **ttest**                   | Returns the t-test metrics using all of the MLContext in the ExploreContext request body.                                                                                                                                   | <p>- <strong>tn1</strong> = count between attribute and target 1.</p><p>- <strong>tn2</strong> = count between attribute and target 2.</p><p>- <strong>tm1</strong> = mean between attribute and target 1.</p><p>- <strong>tm2</strong> = mean between attribute and target 2.</p><p>- <strong>ts1</strong> = variance between attribute and target 1.</p><p>- <strong>ts2</strong> = variance between attribute and target 2.</p><p>- <strong>t</strong> = t test value.</p><p>- <strong>tdf</strong> = t test value degree of freedom.</p><p>- <strong>tp</strong> = t test value probability.</p><p>- <strong>tu</strong> = t test value for unequal variance.</p><p>- <strong>tdfu</strong> = t test value degree of freedom for unequal variance.</p><p>- <strong>tpu</strong> = t test value probability for unequal variance.</p> |
| **ttestmu**                 | Returns the T-test - against a known mean - metrics using all of the MLContext in the ExploreContext request body.                                                                                                          | <p>- <strong>t</strong> = T test value.</p><p>- <strong>tp</strong> = T test value probability.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **anova**                   | Returns the ANOVA metrics using all of the MLContext in the ExploreContext request body.                                                                                                                                    | <p>- <strong>SSb</strong> = sum square between groups.</p><p>- <strong>SSw</strong> = sum square within groups.</p><p>- <strong>Dfb</strong> = degree of freedom between groups.</p><p>- <strong>Dfw</strong> = degree of freedom within groups.</p><p>- <strong>MSb</strong> = mean square between groups.</p><p>- <strong>MSw</strong> = mean square within groups.</p><p>- <strong>F</strong> = F value from F distribution.</p><p>- <strong>Ssgamma</strong> = total sum of square.</p><p>- <strong>Dfgamma</strong>* = total degree of freedom.</p><p>- <strong>P</strong> = probability of F.</p>                                                                                                                                                                                                                                  |
| **chi2**                    | Returns chi-square information using all of the MLContext in the ExploreContext request body.                                                                                                                               | <p>- <strong>chiStat</strong> = chi-square value.</p><p>- <strong>df</strong> = degree of freedom.</p><p>- <strong>pval</strong> = probability.</p><p>- <strong>coef</strong> = contingency coefficient. Additionally, for all the pair wise values the observed count will be returned as a value using the following format for the key. Example: <strong>obs$1:4</strong></p>                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **gain**                    | Returns information gain for a given attribute –input - given another attributes – target.                                                                                                                                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

Note **ztestmu**, **ttestmu**, and **ftestsima** require the following parameters to be set in `MLContext.extraParameters` respectively: **sml\_explore\_ztest\_known\_mu**, **sml\_explore\_ttest\_known\_mu**, and **sml\_explore\_ftest\_known\_sigma**.

## Various Hypothesis Test

Its possible to perform _variations_ on ztest, ftest, ttest, anova, and chi-squared test. Depending on which test needs to be performed the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) must be populated differently.

| Hypothesis Testing Suite                                                                 |                                                                                                                                                                                                             |
| ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hypothesis Test                                                                          | MLContext Content                                                                                                                                                                                           |
| **Z test** Compare means of 2 continuous features.                                       | `inputAttributes`: contains 2 continuous attributes ids                                                                                                                                                     |
| **Z test** Compare mean of 1 continuous feature against a known mean                     | \* `inputAttributes`: contains 1 continuous attribute id \* `extraParameters`: contains one key `sml_explore_ztest_known_mu` with known mean                                                                |
| **Z test** Compare means of a continuous feature conditioned on 2 binary features        | `inputAttributes`: contains 3 attribute id, first one is continuous followed by 2 binary ids                                                                                                                |
| **Z test proportion** Compare two proportions originated from two binary features        | `inputAttributes`: contains 2 binary attribute id                                                                                                                                                           |
| **F test** Compare variance of 2 continuous features.                                    | `inputAttributes`: contains 2 continuous attributes id                                                                                                                                                      |
| **F test** Compare variance of continuous feature against a known variance               | \* `inputAttributes`: contains 1 continuous attribute id \* `extraParameters`: contains one key `sml_explore_ftest_known_sigma` with known variance / sigma                                                 |
| **F test** Compare variances of a continuous feature conditioned on 2 binary features    | `inputAttributes`: contains 3 attribute id, first one is continuous followed by 2 binary ids                                                                                                                |
| **T test** Compare means of 2 continuous features.                                       | `inputAttributes`: contains 2 continuous attributes id                                                                                                                                                      |
| **T test** Compare mean of 1 continuous feature against a known mean                     | \* `inputAttributes`: contains 1 continuous attribute id \* `extraParameters`: contains one key `sml_explore_ttest_known_mu` with known mean                                                                |
| **T test** Compare means of a continuous feature conditioned on 2 binary features        | `inputAttributes`: contains 3 attribute id, first one is continuous followed by 2 binary ids                                                                                                                |
| **Anova** Compare means of 2 or more continuous features                                 | `inputAttributes`: contains 2 or more continuous attribute id                                                                                                                                               |
| **Anova** Compare means of a continuous feature conditioned on 2 or more binary features | \* `inputAttributes`: contains 1 continuous attribute id \* `targets`: contains 1 or more binary attribute id                                                                                               |
| **Chi square** Determine the association between binary features.                        | \*`inputAttributes`: contains 2 or more binary attribute ids \* `targets`: contains 2 or more binary attribute ids see [Chi Square](exploration-api.md#sample-request-response-chi2-example-1) for example. |
| **Chi square** Determine the association between binary features based on category names | `inputAttributes`: list of category name See [Chi Square](exploration-api.md#sample-request-response-chi2-example-2) for example.                                                                           |

### Sample Request/Response Bivariate Statistics

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/explore?metric=bi"

Body:
{"values":[{"targets":[],"inputAttributes":["0","3"],"extraParameters":{}}]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"smlInfo":{"pid":"r1","isDirty":true,"modelsList":[],"modelTypeList":[],"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],"modelAssessments":{},"modelPredictions":{},"hash":-1,"categorySeparator":"$","type":"cpu","creationDate":1488220762857,"lastModificationDate":1488220780484,"loaded":true,"persisted":true},"KSVDMap":{"values":[{"linCorr":0.8180000000000001,"condVariance":30.9294,"covar":0.5135000000000001,"condCount":179.8,"condMean":6.2717}]}}}
```

### Sample Request/Response chi2 Example 1

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/explore?metric=chi2"

Body:
{"values":[{"targets":["12","13","14"],"inputAttributes":["10","11"],"inputAttributeNames":[],"extraParameters":{}}]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"smlInfo":{"pid":"irisP","isDirty":true,"modelsList":[],"modelTypeList":[],"dsList":["Iris_rtlm.csv"],"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],"modelAssessments":{},"modelPredictions":{},"params":{},"hash":-1,"categorySeparator":"$","type":"cpu","creationDate":1588623147023,"lastModificationDate":1588623148044,"loaded":true,"persisted":true,"histogramEnabled":true},"KSVDMap":{"values":[{"df":2.0,"obs$11:12":0.0,"coef":0.8409000000000001,"pval":0.0,"chiStat":150.0,"obs$10:14":0.0,"obs$10:13":0.0,"obs$10:12":50.0,"obs$11:13":50.0,"obs$11:14":50.0}]}}}
```

### Sample Request/Response chi2 Example 2

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/dice/explore?metric=chi2"

Body:
{"values":[{"targets":[],"inputAttributes":["d1","d2","d3","d4","d5"],"inputAttributeNames":[],"extraParameters":{}}]}


Response:
{"statusCode":"OK","statusString":"OK","values":{"smlInfo":{"pid":"dicep","isDirty":true,"modelsList":[],"modelTypeList":[],"dsList":["dice5.csv"],"attributeNames":["d1$1","d1$5","d1$6","d1$3","d1$4","d1$2","d2$3","d2$2","d2$5","d2$6","d2$4","d2$1","d3$2","d3$1","d3$6","d3$4","d3$3","d3$5","d4$5","d4$2","d4$3","d4$6","d4$4","d4$1","d5$1","d5$2","d5$3","d5$4","d5$6","d5$5"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29],"attributeTypes":["B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B"],"modelAssessments":{},"modelPredictions":{},"params":{},"hash":-1,"categorySeparator":"$","type":"cpu","creationDate":1589219373647,"lastModificationDate":1589219374612,"loaded":true,"persisted":true,"histogramEnabled":false},"KSVDMap":{"values":[{"obs$d1:4":174.0,"obs$d2:3":180.0,"obs$d3:2":179.0,"obs$d4:2":155.0,"obs$d5:1":169.0,"obs$d1:3":156.0,"obs$d2:2":162.0,"obs$d3:1":175.0,"obs$d4:3":179.0,"obs$d5:2":172.0,"obs$d1:2":161.0,"obs$d2:1":145.0,"obs$d4:4":196.0,"obs$d5:3":164.0,"df":20.0,"obs$d1:1":182.0,"obs$d4:5":166.0,"obs$d5:4":177.0,"obs$d3:6":155.0,"obs$d2:6":178.0,"obs$d3:5":174.0,"pval":0.20470000000000002,"obs$d1:6":149.0,"obs$d2:5":141.0,"obs$d3:4":168.0,"obs$d1:5":178.0,"obs$d2:4":194.0,"obs$d3:3":149.0,"obs$d4:1":158.0,"obs$d4:6":146.0,"obs$d5:5":156.0,"obs$d5:6":162.0,"coef":0.0334,"chiStat":24.9148}]}}}
```

## VIF Exploration

This rest endpoint return the variance inflation factor for a list of features. This API function is asynchronous. If it succeeds, it returns a 202 response, along with a Location header that specifies the job URL. For more information about SymetryML asynchronous job please refer the section on [Symetry Job](symetryml-job-information.md).

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/vif [body=ExploreContext]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | OK                  | Job accepted.                                                                                                                               |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

The response is a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap), the key are the input index as specified in the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) body and the value is the variance inflation factor for that index.

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/vif"

Body:
{"values":[{"targets":[],"inputAttributes":["2","4"]}]}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job Response:
KSVSMap
```

## PCA Exploration

This REST endpoint allows for the request of the principal component analysis (PCA) of a set of attributes.

This API function is asynchronous. If it succeeds, it returns a 202 response, along with a Location header that specifies the job URL. For more information about SymetryML asynchronous job please refer the section on [Symetry Job](symetryml-job-information.md).

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/explorepca?explainedCovariance [body=ExploreContext]
```

### Query Parameters

| Parameter               | Required/Optional? | Description                                                                                                                                                                                                                                                                                                       |
| ----------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **explainedCovariance** | Optional           | This parameter controls how many Eigen vectors/values will be returned in term of explained covariance. Default is 1 which correspond to 100% of explained covariance. Note that in order to avoid sending too much large response body, the maximum number of Eigen values/vectors that will be returned is 100. |

### Request Body

The request body is gonna be a MLContext that contains information about the inputs to use. It can also contains the following extra parameter `pcaNumDimension` to specify a maximum number of dimension to return. The default value is **100**.

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                    |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

The response contains three keys:

* pcaVectors
* pcaValues
* pcaSumValues

The value for the pcaVectors and pcaValues keys is a Matrix stored in a DataFrame JSON data structure. The pcaSumValues key consists of a number that is the sum of all the Eigen values. See [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) for information on the DataFrame JSON structure.

| HTTP Response Entity | Example                                                                                                                                           |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **pcaVectors**       | A [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) containing the PCA vectors.                                                |
| **pcaValues**        | A [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) containing the PCA values.                                                 |
| **pcaSumValues**     | A number representing the total sum of the PCA values. You can use that number to compute the % of covariance explained by a given Eigen vectors. |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/explorepca"

Body:
{"values":[{"targets":[],"inputAttributes":["2","4"]}]}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job Response:
{pcaVectors, pcaValues, pcaSumValues}
```

## SVD Exploration

This REST endpoint allows to get the singular values decomposition (SVD) of a set of attributes.

This API function is asynchronous. If it succeeds, it returns a 202 response, along with a Location header that specifies the job URL. For more information about SymetryML asynchronous job please refer the section [Symetry Job](symetryml-job-information.md).

The request body needs to contains the list of input attributes id to be used while computing the singular value decomposition. Refer to section [ExploreContext](appendix-a-json-data-structure-schema.md#explorecontext-json) for details.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/exploresvd [body=ExploreContext]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                    |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                            | Example                                                                                                                                                                                                                             |
| --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | A map\<String, String> map containing singular values along their attributes names. Each key/value pair in the result map contains: **key** = a SVD computed number, that is a singular value **value** = The name of the attribute |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/exploresvd"

Body:
{"values":[{"targets":[],"inputAttributes":["2","4", "3", "5"]}]}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job Response:
KSVSMAP{}
```

## SVD Features Selection

This REST endpoint allows to use singular value decomposition to perform feature selection. This endpoint will return a subset of attributes that are not singular based on the SVD algorithms. The SVD algorithms might be used multiple time internally to eventually reach the solution.

This API function is asynchronous. If it succeeds, it returns a 202 response, along with a Location header that specifies the job URL. For more information about SymetryML asynchronous job please refer the section [Symetry Job](symetryml-job-information.md).

The request body needs to contains the list of input attributes id to be used while computing the singular value decomposition. Refer to section [ExploreContext](appendix-a-json-data-structure-schema.md#explorecontext-json) for details.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/exploresvdfs [body=ExploreContext]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                    |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                            | Example                                                                                                                                                                                                                             |
| --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | A map\<String, String> map containing singular values along their attributes names. Each key/value pair in the result map contains: **key** = a SVD computed number, that is a singular value **value** = The name of the attribute |

## Density Estimates

This method returns the density estimate / histogram for any `continuous` or `binary` attributes in your SymetryML project. Histogram building must have been enabled - for the wanted symetry project - before invoking this rest endpoint. Histogram building for a project can be enabled by 2 means:

* When creating the project with the `enableHistogram` query parameter. See [Create Project](symetryml-projects-rest-api.md#create-project-query-parameters) endpoint.
* Explicitly invoking the [Enable Histogram](miscellaneous-api.md#enable-histogram) rest endpoint on any given project.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/densityEstimate [body=ExploreContext]
```

### MLContext Parameters for Density Estimates

For each features / attributes for which histogram are requested a [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) is needed inside the [ExplorationContext](appendix-a-json-data-structure-schema.md#explorecontext-json). The following tables describes the extra parameters can be used:

| Parameter      | Required / Optional | Description                                  |
| -------------- | ------------------- | -------------------------------------------- |
| **hist\_bins** | Required            | Number of bins in the histogram              |
| **hist\_max**  | Optional            | Minimum value of the histogram on the x axis |
| **hist\_min**  | Optional            | Maximum value of the histogram on the x axis |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                    |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                                     | Example                                                                   |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| [**DensityList**](appendix-a-json-data-structure-schema.md#density-list) | see [sample response](exploration-api.md#sample-request-response-3) below |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/densityEstimate"

Body:
{"values":[{"inputAttributeNames":["sepal_length"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"20"}},{"inputAttributeNames":["sepal_width"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"20"}},{"inputAttributeNames":["petal_length"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"20"}},{"inputAttributeNames":["petal_width"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"20"}},{"inputAttributeNames":["sepal_lengt_b1"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["sepal_lengt_b2"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["sepal_width_b1"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["sepal_width_b2"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["petal_length_b1"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["petal_length_b2"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["petal_width_b1"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["petal_width_b2"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["Iris_setosa"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["Iris_versicolor"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}},{"inputAttributeNames":["Iris_virginica"],"inputAttributes":[],"targets":[],"extraParameters":{"hist_bins":"2"}}]}


Response:
{"statusCode":"OK","statusString":"OK","values":{"densityList":{"values":[{"attrName":"sepal_length","min":4.3,"max":7.9,"width":0.18000000000000002,"histogram":[4.0,5.0,7.0,16.0,9.0,5.0,13.0,14.0,10.0,6.0,10.0,16.0,7.0,11.0,5.0,1.0,4.0,1.0,5.0,1.0]},{"attrName":"sepal_width","min":2.0,"max":4.4,"width":0.12000000000000002,"histogram":[1.0,3.0,4.0,3.0,8.0,14.0,14.0,10.0,26.0,12.0,19.0,12.0,6.0,3.0,9.0,2.0,1.0,1.0,1.0,1.0]},{"attrName":"petal_length","min":1.0,"max":6.9,"width":0.29500000000000004,"histogram":[4.0,33.0,11.0,2.0,0.0,0.0,1.0,2.0,3.0,5.0,12.0,14.0,12.0,17.0,6.0,12.0,7.0,4.0,2.0,3.0]},{"attrName":"petal_width","min":0.1,"max":2.5,"width":0.12,"histogram":[34.0,7.0,7.0,1.0,1.0,0.0,0.0,7.0,3.0,5.0,21.0,12.0,4.0,2.0,17.0,6.0,6.0,3.0,8.0,6.0]},{"attrName":"sepal_lengt_b1","min":0.0,"max":1.0,"width":0.5,"histogram":[67.0,83.0]},{"attrName":"sepal_lengt_b2","min":0.0,"max":1.0,"width":0.5,"histogram":[83.0,67.0]},{"attrName":"sepal_width_b1","min":0.0,"max":1.0,"width":0.5,"histogram":[93.0,57.0]},{"attrName":"sepal_width_b2","min":0.0,"max":1.0,"width":0.5,"histogram":[57.0,93.0]},{"attrName":"petal_length_b1","min":0.0,"max":1.0,"width":0.5,"histogram":[89.0,61.0]},{"attrName":"petal_length_b2","min":0.0,"max":1.0,"width":0.5,"histogram":[61.0,89.0]},{"attrName":"petal_width_b1","min":0.0,"max":1.0,"width":0.5,"histogram":[100.0,50.0]},{"attrName":"petal_width_b2","min":0.0,"max":1.0,"width":0.5,"histogram":[50.0,100.0]},{"attrName":"Iris_setosa","min":0.0,"max":1.0,"width":0.5,"histogram":[100.0,50.0]},{"attrName":"Iris_versicolor","min":0.0,"max":1.0,"width":0.5,"histogram":[100.0,50.0]},{"attrName":"Iris_virginica","min":0.0,"max":1.0,"width":0.5,"histogram":[100.0,50.0]}]}}}

```
