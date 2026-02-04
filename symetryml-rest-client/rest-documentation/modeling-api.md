# Modeling API

## About Model Building

This API function allows for the building of a model by specifying the attribute and the targets to use to build the model. The information about which attributes to use to build the model are passed as part of the body request in the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) JSON data structure. For more information see the section [MLContext Build Parameters](modeling-api.md#mlcontext-build-parameters).

### Real-Time Anomaly Models

SymetryML support 5 anomaly detection algorithms:

* **MFAnomaly**: an anomaly model based on a mixture of isolation forest and SymetryML online random forest algorithm.
* **OSSPCAModel**: Online implementation of Out of sample PCA.
* **HBAModel**: Online model based on histograms of the input features.
* **ECODModel**: Empirical Cumulative Distribution Outlier detection
* **EVTModel**: Extreme value Theory based

These algorithms work in real-time on streaming data and are a type of of unsupervised learning algorithm in the sense that one does not need to provide _positive_ examples of anomalies to the different algorithms. The 3 models use intrinsic values of the inputs features to compute an anomaly scores. To create such models just use the appropriate id - from [Model Algorithm ID](modeling-api.md#model-algorithm-id) - when creating them using [Build Model](modeling-api.md#build-model-rest-api) rest API.

### About Clustering

The following sections describes the KMeans clustering algorithm configuration that needs to be specified when creating a new project.

| Parameter                    | Required / Optional | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **cluster\_rnd\_seed**       | Optional            | Set the seed of the randomizer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **cluster\_features**        | Mandatory           | This is a string containing the features to use for the clustering algorithm. The feature are colon separated.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **cluster\_max\_iterations** | Optional            | Default is **1000**. This control the number of iterations of the kmeans algorithm.                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **cluster\_num\_centroids**  | Optional            | Default is **100**. This parameter control the number of centroid that are kept in real time on the data. Typically this number is much higher than the intended number of cluster - e.g. the target _k_ parameters                                                                                                                                                                                                                                                                                                                                             |
| **cluster\_warmup\_period**  | Optional            | Default is **101**. This parameters control how many tuples of data must be seen before the initial real-time cluster will be constructed using the _cluster\_num\_centroids_ parameter as the k number. Once the initial cluster is created it will just be updated in real-time with each new tuple/row of data. Note that **cluster\_warmup\_period** needs to be greater than **cluster\_num\_centroids**, e.g. **cluster\_num\_centroids**=100 and **cluster\_warmup\_period**=101. If this condition is not meet an error will be raised by the software. |

### About Elastic Net AIC

Akaike information criterion (AIC) is a model selection heuristic which allows the user to select the optimal Eta and Lambda hyperparameters for their Elastic Net models using in-sample data. The AIC implementation in SymetryML is corrected for small sample sizes. This adjustment is commonly referred to as AICc. More detailed description of the AIC and small sample correction can be found [here](https://en.wikipedia.org/wiki/Akaike\_information\_criterion#AICc). In the context of SymetryML, the use of AIC can be selected by specifying the following **extraParameters** of the **MLContext**.

| AIC HyperParameter     | Description                                                                                          |
| ---------------------- | ---------------------------------------------------------------------------------------------------- |
| **aicc**               | Boolean, true to enable AIC. Default is false;                                                       |
| **min\_lambda\_power** | Minimum value of Lambda. Specified as 10 ^ **min\_lambda\_power**. Default is -10.                   |
| **max\_lambda\_power** | Maximum value of Lambda. Specified as 10 ^ **max\_lambda\_power**. Default is 3                      |
| **num\_lambda**        | Number of possible Lambdas between **min\_lambda\_power** and **max\_lambda\_power**. Default is 100 |
| **min\_eta**           | Minimum eta. Default is 0.                                                                           |
| **max\_eta**           | Maximum eta. Default is 1.                                                                           |
| **num\_eta**           | Number of possible Etas between **min\_eta** and **max\_eta**. Default is 11.                        |

Note, when **aicc** is enabled. It’s not necessary to specify the **lambda** and **eta** parameters separately as they will be chosen by the AIC optimizer.

### Model Algorithm Id

The following models are supported. For power regression model to be available a SymetryML project must have some attributes set when it is created. Please consult the [Symetry Job](symetryml-job-information.md#specific-job-information) section.

| Id                 | Algorithm                                                                                                                                                                                                                                    |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **bayes**          | Bayesian Model                                                                                                                                                                                                                               |
| **covest**         | Covariance Estimator                                                                                                                                                                                                                         |
| **ecod**           | ECOD Model                                                                                                                                                                                                                                   |
| **elasticnet**     | Elastic Net                                                                                                                                                                                                                                  |
| **evt**            | EVT model                                                                                                                                                                                                                                    |
| **hmm**            | Hidden Markov Chains                                                                                                                                                                                                                         |
| **km**             | Kaplan Meier                                                                                                                                                                                                                                 |
| **kmeans**         | KMeans Clustering                                                                                                                                                                                                                            |
| **lasso**          | Lasso Regression                                                                                                                                                                                                                             |
| **lr**             | Single Pass Logistic Regression                                                                                                                                                                                                              |
| **lda**            | LDA, Linear Discriminant. Please also consult the section about [Covariance Estimate Model](modeling-api.md#covariance-estimate-model-mlcontext-build-parameters) as its possible to use this type of covariance matrix to build LDA models. |
| **lsvm**           | Linear SVM                                                                                                                                                                                                                                   |
| **lsvr**           | Linear Support Vector Regression                                                                                                                                                                                                             |
| **mc**             | Markov Chains                                                                                                                                                                                                                                |
| **mlda**           | Multi-class LDA, Linear Discriminant                                                                                                                                                                                                         |
| **mlr**            | Multi Linear Regression                                                                                                                                                                                                                      |
| **mqda**           | Quadratic discriminant Analysis                                                                                                                                                                                                              |
| **pcr**            | Principal Component Regression                                                                                                                                                                                                               |
| **plsq**           | Partial Least Square Regression                                                                                                                                                                                                              |
| **powerreg**       | Power Regression                                                                                                                                                                                                                             |
| **ridge**          | Ridge Regression                                                                                                                                                                                                                             |
| **rf\_classifier** | Online Random Forest Classifier                                                                                                                                                                                                              |
| **rf\_regressor**  | Online Random Forest Regression                                                                                                                                                                                                              |
| **rf\_anomaly**    | Anomaly / outlier model based on Online Random forest and isolation forest                                                                                                                                                                   |
| **oospca**         | Online Anomaly / outlier model based on _out of sample PCA_.                                                                                                                                                                                 |
| **hba**            | Online Anomaly / outlier model based on histograms of the input features.                                                                                                                                                                    |

### About Random Forest

In order to use the Online random forest additional parameters must be specified when creating a SymetryProject. Please consult the [Create Project](symetryml-projects-rest-api.md#create-new-symetryml-project) endpoint for details. The following 2 tables describes the hyper parameters available with the SML Random Forest algorithm:

| Mandatory Hyperparameters | Description                       |
| ------------------------- | --------------------------------- |
| **rf\_type**              | rf\_classifier permitted for now. |
| **rf\_target\_name**      | target name                       |

| Optional Hyperparameters | Description                                                                                                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rf\_num\_classes**     | mandatory for classifier, specify number of classes, **default:2**                                                                                                                                         |
| **rf\_features**         | list of columns to use for the Random Forest. If nothing is specified or '\*' is used, then all continuous and binary attributes will be used, beside the target. Values are to be separated by colon _:_. |
| **rf\_always\_split**    | control if split should occurs when a node contains only examples of one class, default is false, should be true for anomaly detection, **default:false**                                                  |
| **rf\_num\_trees**       | number of trees in the forest, **default:10**                                                                                                                                                              |
| **rf\_step**             | Gradient step. **default:1**                                                                                                                                                                               |
| **rf\_complexity**       | Control how complex the trees can grow, typical **default:16**, typical value 8 - 50                                                                                                                       |
| **rf\_max\_split\_time** | Another way of controlling how complex tree can grow. if both `rf_complexity` and `rf_max_split_time` are specified `rf_max_split_time` has priority.                                                      |
| **rf\_missing\_value**   | value to use for missing value, **default:0.0**                                                                                                                                                            |
| **rf\_dirichlet**        | prior for classification prediction, **default .5** for binary classification, **1/n** for multiple class problems                                                                                         |
| **rf\_rnd\_seed**        | Random seed, **default:126**.                                                                                                                                                                              |

### Sample Request/Response

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=irisMeta&type=partition&persist=true
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=irisMeta&type=partition&persist=true

Request Body:
{"rf_target_name":"target","rf_num_classes":"3","rf_complexity":"12"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:rfProject","values":{}}
```

### About Kaplan Meier Model

Kaplan Meier (KM) requires three additional parameters to be specified when creating a SymetryML project. Please consult the [Create Project](symetryml-projects-rest-api.md#create-new-symetryml-project) endpoint for details.

| KM Parameters         | Required / Optional | Description                                  |
| --------------------- | ------------------- | -------------------------------------------- |
| **km\_time\_column**  | Required            | Identifies the time column of your dataset   |
| **km\_event\_column** | Required            | Identifies the event column of your dataset  |
| **km\_group\_column** | Optional            | Identifies the group column of your dataset. |

### Sample Request/Response

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=km1&type=cpu&persist=true

Request Body:
{"time_column":"time","group_column":"groups","event_column":"dead"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:km1","values":{}}
```

After enabling your project with KM parameters, building a KM model is as simple calling the [Build Model](modeling-api.md#build-model-rest-api) REST Endpoint. At this stage you can optionally set the `alpha` parameter which controls the width of your confidence intervals. This is done by setting `alpha` in the `extraParameters` field of the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json).

### About Single Pass Logistic Regression

Single Pass Logistic Regression (LR) requires parameters to be specified when creating a SymetryML project. Please consult the [Create Project](symetryml-projects-rest-api.md#create-new-symetryml-project) endpoint for details.

| LR Parameter           | Required / Optional | Description                                                                                                                                       |
| ---------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **lr\_target\_column** | Required            | The target attribute of the LR model                                                                                                              |
| **lr\_features**       | Optional            | Input attributes of the LR model. Use **\*** to use all attributes which are not the target. Otherwise, use colon separated string of attributes. |

### Sample Request/Response

```
Request:
POST url=http://charm:8080/symetry/rest/c1/projects?pid=lr1&type=cpu&persist=true

Request Body:
{"lr_target_column":"target","lr_features":"a1:a2:a3"}

Response:
{"statusCode":"CREATED","statusString":"Project Created with id:lr1","values":{}}
```

### About Power Regression Model

Linear regression assumes that the target attribute is normally distributed. While in real world scenarios this is often not the case, we can still improve the performance of the regression model by transforming the target into something resembling a normal distribution. The method of transforming that is used within SymetryML is the Yeo-Johnson transformation.

SymetryML projects with power transformation enabled will automatically create new attributes representing various power transformations of the target so that they can be modeled against the existing features. These additional target features will have a prefix and separator to easily pinpoint them. (e.g `pt_sepal_width^-2.0`, `pt_sepal_width^-1.8`, `pt_sepal_width^-1.6`, …, `pt_sepal_width^1.8`, `pt_sepal_width^2.0`). It’s possible to configure the prefix and separator - `pt_` and `^` in our previous example. Please refer to the [Power Regression Parameters](modeling-api.md#power-regression-parameters) table for details.

Once a project has learned these transformations, a Power Regression model can be build which would select the optimal transformed target.

To enable Power Regression, please specify the following parameters when creating your project:

#### Power Regression Parameters

| Parameter                       | Type   | Description                                                    |
| ------------------------------- | ------ | -------------------------------------------------------------- |
| **sml\_project\_power\_column** | String | Name of column to transform                                    |
| **sml\_project\_power\_min**    | Number | Minimum value of the power parameter                           |
| **sml\_project\_power\_max**    | Number | Maximum value of the power parameter                           |
| **sml\_project\_power\_steps**  | Number | Number of intervals between min and max of the power parameter |

Example of of MLContext used when creating a project that use power transformation:

```
POST http://charm:8080/symetry/rest/c1/projects>?pid=pt1&type=cpu&persist=false

BODY:
{"sml_project_power_column":"sepal_width","sml_project_power_steps":"5","sml_project_power_min":"-1","sml_project_power_max":"2"}
```

| Power Regression Parameters     | Type    | Description                             |
| ------------------------------- | ------- | --------------------------------------- |
| **sml\_project\_power\_column** | String  | Name of the target column               |
| **sml\_project\_power\_min**    | Float   | minimum power to use                    |
| **sml\_project\_power\_max**    | Float   | maximum power to use                    |
| **sml\_project\_power\_steps**  | Integer | steps between minimum and maximum power |

Power transformation prefix and suffix can be configured in the `/opt/symetry/symetry-rest.txt` configuration file. Please consult the [Installation Guide Symetry Control Configuration](../../guides/installation-guide/#about-the-symetryml-configuration) for details on configuring that file.

| Parameter                           | Description      |
| ----------------------------------- | ---------------- |
| **rtlm.option.sml.power.separator** | Default is `^`   |
| **rtlm.option.sml.power.prefix**    | Default is `pt_` |

## MLContext Build Parameters

### MLContext Build Parameters for Matrix

When SymetryML builds new model it might use Matrix inversion. These operations are performed using an implementation of LAPACK (DGETRF and DGETRI) using some third party library using CPU and / or GPU. Matrix inversion can lead to numerical instability problem. The following parameters affect how matrix inversion is computed.

| Parameter                        | Type                       | Description                                                                                                                                                                                                                                                                                                    |
| -------------------------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **sml\_rcond\_use**              | Boolean: ‘true’ or ‘false’ | Controls whether SymetryML conducts numerical instability (i.e., reciprocal condition number validation) when performing matrix inversion when building a model.                                                                                                                                               |
| **sml\_rcond\_tolerance**        | Number                     | By default to the accepted tolerance for the reciprocal condition number validation is 1e-14. Using this parameter, you can set it to lower values. However, be sure you understand the implications and assess your model carefully, as you might get a numerically unstable model when modifying this value. |
| **matrix\_use\_pseudoinv**       | boolean                    | Use matrix pseudo inverse algorithm - with SVD - when computing matrix inverse in model like MLR, LDA and QDA.                                                                                                                                                                                                 |
| **sml\_disable\_error\_logging** | boolean                    | Default is true, This disable logging of error when performing matrix solving operations. This functionality is used by many regression models.                                                                                                                                                                |

### Other MLContext Build Parameters

There are other parameters that control various model building

| Parameter                          | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **clip\_negative\_predictions**    | Boolean | For regression model and if set to `true` it will clip negative prediction to zero.                                                                                                                                                                                                                                                                                                                                                                  |
| **sml\_explore\_ztest\_known\_mu** | Number  | This parameter is used to pass the _known variance_ when performing the _ztest_ against a known variable.                                                                                                                                                                                                                                                                                                                                            |
| **sml\_det\_norm\_use**            | Boolean | This allows to avoid numerical instability when computing determinant of large matrix. Determinant are needed when building QDA model with [partitioned projects](symetryml-projects-rest-api.md#about-partitioned-projects). This parameter control how determinant is computed by using a sum of natural logarithms - instead of multiplication on the diagonal of the decomposed inverted matrix.                                                 |
| **alpha**                          | Number  | Alpha parameter when building a Ridge Regression model                                                                                                                                                                                                                                                                                                                                                                                               |
| **matrix\_use\_pseudoinv**         | Boolean | Use matrix pseudo inverse algorithm - with SVD - when computing matrix inverse in model like MLR, LDA and QDA.                                                                                                                                                                                                                                                                                                                                       |
| **sml\_model\_svm\_nu**            | Number  | `nu` parameter for LSVM model.                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **evt\_anomaly\_enabled**          | Boolean | Enable this model to use the extreme value theory algorithm to mark prediction as anomaly or not. This functionality is only available for regression and anomaly model. When enabled an additional prediction result key is returned : `evt_is_anomaly`. Please consult [https://hal.archives-ouvertes.fr/hal-01640325/document](https://hal.archives-ouvertes.fr/hal-01640325/document) for background on the extreme value theory algorithm used. |
| **laplace\_factor**                | Number  | Laplace smoothing factor. Default is `1`. See [Additive smoothing](https://en.wikipedia.org/wiki/Additive_smoothing).|
| **use\_log\_likelihood**           | Boolean | Default is `true`. Used to avoid numerical underflow caused by product of probabilities. |

### KMeans Clustering MLContext Build Parameters

When building Kmeans clustering model the following hyper parameters are required:

| Parameter                   | Required / Optional | Description                                        |
| --------------------------- | ------------------- | -------------------------------------------------- |
| **kmeans\_k**               | Required            | Number of clusters                                 |
| **kmeans\_max\_iterations** | Optional            | default:1000, Number of iterations.                |
| **kmeans\_rnd\_seed**       | Optional            | default:current time, the seed for the randomizer. |

### Elastic Net MLContext Build Parameters

Elastic Net is an extension of the classic MLR algorithm which adds regularization as a way to combat overfitting of your training data. It combines the regularization methods of both Ridge (L-2 norm regularization) and Lasso (L-1 norm regularization). While all three models feature the regularization parameter Lambda, Elastic Net contains one extra parameter, ETA, which controls how close the regularization should be to a pure Ridge regression model (ETA = 0) versus a pure Lasso approach (ETA = 1)

When building Elastic Net, Lasso or Ridge regression model the following hyper parameters are required:

| Parameter  | Type   | Description                     |
| ---------- | ------ | ------------------------------- |
| **lambda** | Number | Lambda parameter of Elastic Net |
| **eta**    | Number | Eta parameter of Elastic Net    |

Optional parameters for Elastic Net are the following:

| Parameter       | Type    | Description                                                                             |
| --------------- | ------- | --------------------------------------------------------------------------------------- |
| **maxiter**     | Number  | Maximum number of iteration                                                             |
| **tolerance**   | Number  | Convergence tolerance. Defaults to 1e-5. Increase if the model convergence is too slow. |
| **standardize** | Boolean | Transform each feature to the same scale (Z-score normalization)                        |
| **centerY**     | Boolean | Center target around zero                                                               |
| **debias**      | Boolean | Debias the estimator                                                                    |

The combined effect of centering the targets (centerY = true) as well as standardize the features (standardize = true) is to get rid of the intercept.

Lastly, Elastic Net models initial weights can be specified in the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) in the following format:

| Parameter           | Type   | Description                     |
| ------------------- | ------ | ------------------------------- |
| **ATTRIBUTE\_NAME** | Number | The value of the initial weight |

### ECOD Model MLContext Build Parameters

ECOD stands for Empirical-Cumulative-distribution-based Outlier Detection (https://arxiv.org/pdf/2201.00382.pdf). It uses the CDF of each feature to estimate tail probabilities per dimension for each data point. An outlier score is calculated by aggregating probabilities across dimensions.

| Parameter                    | Description                                                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **ecod\_anomaly\_threshold** | The value of threshold to use to flag an anomaly. If not provided, will choose a value based on anomaly probability per dimension desired. |
| **use\_skewness**            | Using the skewness of each feature in the aggregating strategy.                                                                            |
| **desired\_prob\_feats**     | Anomaly probability per desired dimension.                                                                                                 |

### EVT Model MLContext Build Parameters

A typical anomaly model outputs an anomaly score for a particular instance with those instances that are further from nominal receiving a higher score. The decision whether to flag something as an anomaly essentially becomes a process of choosing a threshold for this score. Scores above the threshold will be flagged as anomalies and anything below it will be deemed as nominal.

If the distribution of your input data does not change, the threshold method might be sufficient for your needs. However, in the case of dynamically changing distributions more robust approaches should be used. Once such approach is to leverage Extreme Value Theorem (EVT) to compute a distribution of extreme values which we can later be queried to determine whether or not a particular instance is an anomaly. Additional information on the use of EVT for anomaly detection can be found [here](https://hal.archives-ouvertes.fr/hal-01640325/document)

| Parameters                | Required / Optional | Description                                                                                                                                                                                            |
| ------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **evt\_warmup\_size**     | Required            | Size of the initial set of scores used to build the Pareto Distribution.                                                                                                                               |
| **evt\_alpha**            | Required            | The probability of an observation to be greater than the threshold Zq is smaller than alpha.                                                                                                           |
| **evt\_quantile**         | Required            | This parameter is the original chosen quantile from which we assume we are in the tail of the score distribution. Any value above that can be used to fit the tail Generalized Pareto distribution.    |
| **evt\_tdigest**          | Optional            | Allows the specified `evt_quantile` to dynamically adjust with the addition of new data. If false, the quantile will be based on the original `evt_warmup_size` number of rows. **Default is false.**  |
| **evt\_window\_size**     | Optional            | The size of the look back window used to update the Pareto Distribution.                                                                                                                               |
| **evt\_two\_sides**       | Optional            | If false, only values above `evt_quantile` will be treated as exceedances. Otherwise, uppwer exceedances (above `evt_quantile`) and lower exceedances (`1 - evt_quantile`) will be tracked seperately. |
| **evt\_seed**             | Optional            | Seed for initial distribution.                                                                                                                                                                         |
| **evt\_max\_exceedances** | Optional            | The maximum number of values used to fit the Pareto Distribution.                                                                                                                                      |

### Principal Component Regression Model MLContext Build Parameters

Principal component regression is a technique based on principal component analysis (PCA). To be more precise, projected features are used in conjunction with linear regression. Two flavors are available.

* I - The traditional approach where the first q principal components are kept, and a linear regression is applied to this q-dimensional space.
* II - An augmented features space is created using the p original features and the p principal components. A model is created by applying Lasso regression. The assumption that the first q principal components are the most important is not necessarily true and other principal components may correlate also with the target. This approach enables to pick up such contributions.

|                        | Description                                                                                                                                              |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **pcr\_type**          | `qfirst` - Using the approach with the first q components. Or `full` - Using the approach with the original features as well as the principal components |
| **lambda**             | If `pcr_type` is `full`, specify value of lambda. **Default is 1**                                                                                       |
| **aicc**               | If `pcr_type` is `full`, use aicc true or false to automaticaly find the best value of lambda or not.                                                    |
| **min\_lambda\_power** | If **pcr\_type** is `full` and `aicc=true`, smallest power of **lambda** to test. **Default is -10.**                                                    |
| **max\_lambda\_power** | If **pcr\_type** is `full` and `aicc=true`, largest power of lambda to test. **Default is NaN** which mean the value will be decided automatically.      |
| **num\_lambda**        | If **pcr\_type** is `full` and `aicc=true`, how many values of lambda to test. **Default is 100.**                                                       |
| **maxiter**            | If **pcr\_type** is `full`, maximum number of iterations for Lasso. **Default is 1000.**                                                                 |
| **tolerance**          | if **pcr\_type** is `full` tolerance for Lasso. **Default is 1e-05.**                                                                                    |
| **standardize**        | iI **pcr\_type** is `full` standardize or not. **Default is true.**                                                                                      |
| **centerY**            | If **pcr\_type** is `full` center the target or not. **Default is false.**                                                                               |
| **q**                  | iIf using **pcr\_type**=`qfirst`, the number of principal components to keep.                                                                            |

### Partial Least Square Regression Model MLContext Build Parameters

Partial least square regression creates a new latent space of size q where q is smaller than p the number of features in which to do the regression. In the approach available, only the coefficients projected back to the original p dimensions are obtained.

| Parameter       | Description                                                                                         |
| --------------- | --------------------------------------------------------------------------------------------------- |
| **ddof**        | The degree of freedom to use for calculation. **Default is 0.**                                     |
| **oneq**        | Make the calculation for only one size of latent space or all size up to max. **Default is false.** |
| **standardize** | If **pcr\_type** is `full` **standardize** or not. **Default is true.**                             |
| **q**           | The maximum size of the underlying latent space.                                                    |

### Covariance Estimate Model MLContext Build Parameters

Covariance estimate deals with the problem of estimating the actual covariance matrix when only the empirical covariance matrix is available (coming from a sample of the multivariate distribution). The covariance estimators available use different regularization methods.

|                        | LDA | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ---------------------- | --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **estimate\_type**     | y   | `graph_lasso` or `l2_reg_thinkhonov` or `l2_reg_sig` or `identity_mixture` or `eigen_trunc.` All Model except `graph_lasso`can be used with the LDA model.                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **tuning\_type**       | y   | <p>if <strong>estimate_type</strong> is <code>graph_lasso</code> or <code>l2_reg_sig</code>, the regularization can be provided by some automatic tuning.<br></p><ul><li><strong>ebic:</strong> if <strong>estimate_type</strong> is <code>graph_lasso</code>, this is the possible automatic tuning.</li><li><strong>rblw:</strong> if <strong>estimate_type</strong> is <code>l2_reg_sig</code>, this is one possible automatic tuning. Can be used with LDA.</li><li><strong>oas:</strong> if <strong>estimate_type</strong> is <code>l2_reg_sig</code>, this is one possible automatic tuning. Can be used with LDA.<br></li></ul> |
| **alpha**              | y   | if **estimate\_type** is `l2_reg_sig` or `identity_mixture`, the **alpha** parameter if tuning not set. **Default is 0.1**.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **gamma**              |     | if **tuning** is `ebic`, value of the **gamma** parameter. **Default is 0.5.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **tolerance\_lasso**   |     | The tolerance for the intermediate Lasso problem when **estimate\_type** is `graph Lasso`. By **default it is 0.1 X tolerance**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **ddof**               |     | The degree of freedom to use for calculation. **Default is 0**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **lambda**             | y   | For **estimate\_type** equals to `graph_lasso` or `l2_reg_thinkhonov`, value of **lambda**. **Default is 1.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **q**                  | y   | For **estimate\_type=** `eigen_trunc`, how many eigenvectors to keep.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **maxiter**            |     | if **estimate\_type=**`graph_lasso` maximum number of iterations for Lasso. **Default is 1000.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **tolerance**          |     | if **estimate\_type =**`graph_lasso`, tolerance for for global probelm. **Default is 1e-05**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **min\_lambda\_power** |     | if **estimate\_type =**`graph_lasso`, smallest power of lambda to test. **Default is -10.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **max\_lambda\_power** |     | if **estimate\_type =**`graph_lasso`, largest power of lambda to test. **Default is NaN** means it will be decided automatically.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **num\_lambda**        |     | If **estimate\_type =**`graph_lasso`, how many values of lambda to test. **Default is 100**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **initialize\_gl**     |     | <p>How to initialize the graphical lasso iteration. Choices are:</p><ul><li><code>lasso_path</code> Start from a large enough value of lambda such that all the coefficients are zero and reduce until getting to the wanted lambda</li><li><code>inv</code> Initialize using the inverse of the covariance</li><li><code>last_sol</code> First iteration uses <code>inv</code> and then using previous iteration solution as the starting point</li><li><code>lin_reg</code> Initialize the coefficients with the value of the linear regression solution</li><li>A matrix of initial coefficients</li></ul>                          |

## Build Model Rest API

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/build?modelid={modelid}&algo={algorithm} [body=MLContext]
```

### Query Parameters

| Parameter     | Required / Optional | Description                                                  |
| ------------- | ------------------- | ------------------------------------------------------------ |
| **modelid**   | Required            | ID to assign to the new model.                               |
| **algo**      | Required            | Algorithm to use to build the model. See Model Algorithm Id. |
| **svdreduce** | Optional            | Use SVD feature selection                                    |

### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                                                                                                  |
| ---------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED              | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. `{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}` |
| **500**          | INTERNAL SERVER ERROR | If the server refuses to accept the new job, it notifies the client with the error "Job execution was refused by server."                                                                    |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/build?algo=lda&modelid=testLDA"

Body:
{"targets":["3"],"inputAttributes":["0","1","2"]}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/5

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/5

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{}}
```

## Features Reduction Model Building

This API function allows for the building of an optimized model (aka reduced models), where the SymetryML service builds a model using the best attributes for the target specified in the MLContext of the request body. Like the model-building request, the response contains a Location header for the job that was created to service this request. Information about these jobs can be requested and are described in the [Symetry Jobs](symetryml-job-information.md) chapter.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/reduce?heuristic={heuristic}&modelid={modelid}&algo={algorithm}&delta [body=MLContext]
```

### Query Parameters

| Parameter         | Required / Optional | Description                                                             |
| ----------------- | ------------------- | ----------------------------------------------------------------------- |
| **Heuristic**     | Required            | Used when optimizing the model. Currently, only "default" is supported. |
| **Modelid**       | Required            | ID to assign to that model.                                             |
| **Algo**          | Required            | Algorithm used to build the model (currently lda is supported).         |
| **Delta**         | Optional            | Minimum delta to decide whether one model is better than another.       |
| **numIterations** | Optional            | Specify the number of iterations used to refine the best model.         |

### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                                                                                                  |
| ---------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED              | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. `{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}` |
| **500**          | INTERNAL SERVER ERROR | If the server refuses to accept the new job, it notifies the client with the error "Optimize Job execution was refused by server."                                                           |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/reduce?modelid=ldaOptimized&algo=lda&heuristic=default&delta=0.001&numIterations=5"

Body:
{"targets":["13"],"inputAttributes":["0","1","2","3","4","5","6","7","8","9","10","11","12","14"]}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/7

Response:
{"statusCode":"ACCEPTED","statusString":"Reduce Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/7

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{}}
```

## Building Sequence Models

Markov Chain Models and Hidden Markov Models are built differently. First, the project must be marked as a “sequence” project. After some data is learned, you can build Markov Chains, Hidden Markov Models, or both. These types of models have input attributes only; they do not have a target.

For Markov Chains, you input attributes to specify the sequence attributes to use. For Hidden Markov Model, you also specify the hidden state that must be categorical. The Observed state can be continuous or categorical. If it is a continuous attribute, normal distribution will be assumed, mean and standard deviation of the data is used.

## KMeans Clustering Model Optimization

This rest endpoint allows to find the optimal _k_ for your KMeans clustering model by building many model and then returning the silhouette and WSSSE metrics so that both the silhouette and elbow method can be used to decide which is the optimal _k_ number of cluster that should be used.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/kmeansOptimize [body=MLContext]
```

### MLContext Build Parameters

| Parameter                   | Required / Optional | Type     | Description                                                         |
| --------------------------- | ------------------- | -------- | ------------------------------------------------------------------- |
| **kmeans\_k\_min**          | Required            | Integer  | The minimum _k_ to test                                             |
| **kmeans\_k\_max**          | Required            | Integer  | The maximum _k_ to test                                             |
| **kmeans\_max\_iterations** | Optional            | Integer  | The maximum number of iteration for the KMean clustering algorithm. |
| **kmeans\_rnd\_seed**       | Optional            | Optional | The random seed to use                                              |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | OK                  | Job accepted.                                                                                                                               |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response KMeans Optimizer

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/p1/kmeansOptimize"

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":[],
    "targetAttributeNames":[],
    "extraParameters":
    {
        "kmeans_k_min":"2",
        "kmeans_rnd_seed":"42",
        "kmeans_k_max":"5",
        "kmeans_max_iterations":"1000"
    }
}


Response:
Location: http://charm:8080/symetry/rest/c1/jobs/2

Response once job is finished:
{
    "statusCode":"OK",
    "statusString":"Job is finished",
    "values":{
        "KSVSMap":{
            "values":[
            {
                "kmeans_k_min":"2",
                "kmeans_rnd_seed":"42",
                "kmeans_5_silhouette":"0.24742236190463987",
                "kmeans_5_wssse":"24038.895607683746",
                "kmeans_4_silhouette":"0.2615571675703183",
                "kmeans_3_wssse":"34178.95949179863",
                "kmeans_k_max":"5",
                "kmeans_2_silhouette":"0.24099295660249936",
                "kmeans_4_wssse":"26852.68838040368",
                "kmeans_3_silhouette":"0.21947966456276002",
                "kmeans_max_iterations":"1000",
                "kmeans_2_wssse":"40316.897273276154"
            }
            ]
        }
    }
}
```

## Model Information

This API function retrieves specific information about a model. A [ModelInfo](appendix-a-json-data-structure-schema.md#modelinfo) JSON data structure is returned. The response contains the type of model, attributes, and targets used by the model, along with specific information based on the type of model (info field).

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/models/{modelid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                | Description                       |
| ------------------------------------------------------------------- | --------------------------------- |
| [**ModelInfo**](appendix-a-json-data-structure-schema.md#modelinfo) | Contains information about model. |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/projects/irisTest/models/ldaOptimized"

Response:
{"statusCode":"OK","statusString":"OK","values":{"modelInfo":{"modelType":"lda","attributeNames":["petal_length","petal_width","sepal_width","petal_length_b1","sepal_lengt_b1","sepal_width_b1","petal_width_b2","sepal_length"],"attributeIndexes":[2,3,1,6,4,5,7,0],"info":{"buildStatus":"\"No Error\"","z0":"18.9666","z1LTz0":"false","z":"\"l0 \u003c- c(3.93801 ,2.43275 ,-8.92229 ,-15.9048 ,0.997005 ,-1.15579 ,-4.66130 ,59.3254 )\n\"","buildTimeNanosec":"6553273","OverallContribution":"4.49040"},"predictInfo":["resZ","res","normZ"],"scoreInfo":["normZ"],"buildTime":54,"modelBuilt":140,"targetNames":["Iris_versicolor"],"targetindexes":[13],"modelId":"ldaOptimized"}}}
```

### Model Info Map Key

The following table enumerates the additional specific keys/value pairs that are returned when asking for model information based on the type of model.

| Model Type                                      | Model Keys                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LDA** & **MLDA**                              | <p>model<br>distance<br>z0<br>z<br>Overall contribution for each input parameters</p>                                                                                                                                                                                                                                                                                                                                       |
| **MLR**, **Ridge**, **Elastic Net** & **Lasso** | <ul><li>model Betas</li><li>the <a href="appendix-a-json-data-structure-schema.md#modelinfo">ModelInfo</a> <code>extra</code> field will also contains the <a href="modeling-api.md#mlr-ridge-elasticnet-lasso-extra">coefficient information</a></li></ul>                                                                                                                                                                 |
| **LR**                                          | <ul><li>multiclass - <code>false</code> for a binary classifier, <code>true</code> for a multiclass classifier</li><li>the <a href="appendix-a-json-data-structure-schema.md#modelinfo">ModelInfo</a> <code>extra</code> field will also contains the <a href="modeling-api.md#model-extra-lr">lr coefficient information</a></li></ul>                                                                                     |
| **QDA**                                         | Model                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **LSVM**                                        | <p>svmNu<br>svmGamma<br>svmOmegas</p>                                                                                                                                                                                                                                                                                                                                                                                       |
| **LSVR**                                        | <p>svmNu<br>svmGamma<br>svmOmegas<br></p>                                                                                                                                                                                                                                                                                                                                                                                   |
| **Bayes**                                       |                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **ECOD**                                        | See [model extra ECOD](modeling-api.md#model-extra-ecod)                                                                                                                                                                                                                                                                                                                                                                    |
| **EVTModel**                                    |                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Kmeans**                                      | See [model extra Kmeans](modeling-api.md#model-extra-kmeans)                                                                                                                                                                                                                                                                                                                                                                |
| **Markov Chains**                               | <p>Transition Matrix formatted as follows:<br>{tm1$STATE:$:to$STATE – where STATE corresponds for Markov process state.</p>                                                                                                                                                                                                                                                                                                 |
| **Hidden Markov Model**                         | <p>Transition Matrix formatted as follows:<br>{HIDDEN_tm1$STATE:$:HIDDEN_tm0$STATE – where HIDDEN/STATE corresponds for Markov process state group and state respectively.<br>Emission Matrix formatted as follows<br>[HIDDEN_t0$STATE:$:OBSERVED$STATE – where HIDDEN/STATE corresponds for Markov process state-group and state respectively, while OBSERVED/STATE corresponds to the observed state-group and state.</p> |
| **Random Forest**                               | See [model extra RF](modeling-api.md#model-extra-rf)                                                                                                                                                                                                                                                                                                                                                                        |
| **Kaplan Meier**                                | See [model extra KM](modeling-api.md#model-extra-km)                                                                                                                                                                                                                                                                                                                                                                        |
| **Principal Component Regression**              | see [model extra Principal Component Regression](modeling-api.md#model-extra-principal-component-regression)                                                                                                                                                                                                                                                                                                                |
| **Covariance Estimator**                        | see [model extra Covariance Estimator](modeling-api.md#model-extra-covariance-estimator)                                                                                                                                                                                                                                                                                                                                    |
| **Partial Least Square Regression**             | see [model extra Partial Least Square Regression](modeling-api.md#model-extra-partial-least-square-regression)                                                                                                                                                                                                                                                                                                              |

#### Model Extra - EVTModel

| Key                            | Description                                                                                                                                                                                            |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **evt\_anomaly\_enabled**      | Set to `true` to enable EVT for this model                                                                                                                                                             |
| **evt\_alpha**                 | The probability of an observation to be greater than the threshold Zq is smaller than alpha.                                                                                                           |
| **evt\_quantileevt\_quantile** | This parameter is the original chosen quantile from which we assume we are in the tail of the score distribution. Any value above that can be used to fit the tail Generalized Pareto distribution.    |
| **evt\_tdigest**               | Allows the specified `evt_quantile` to dynamically adjust with the addition of new data. If false, the quantile will be based on the original `evt_warmup_size` number of rows.                        |
| **evt\_window\_size**          | The size of the look back window used to update the Pareto Distribution.                                                                                                                               |
| **evt\_seed**                  | Seed for initial distribution.                                                                                                                                                                         |
| **evt\_two\_sides**            | If false, only values above `evt_quantile` will be treated as exceedances. Otherwise, uppwer exceedances (above `evt_quantile`) and lower exceedances (`1 - evt_quantile`) will be tracked seperately. |
| **evt\_max\_exceedances**      | The maximum number of values used to fit the Pareto Distribution.                                                                                                                                      |

#### Model Extra - MLR, Ridge, ElasticNet, Lasso

| Key                 | Description                       |
| ------------------- | --------------------------------- |
| **attribute**       | Attributes names                  |
| **coefficient**     | Attribute coefficient             |
| **std.error**       | standard error of the coefficient |
| **scores**          | _t-value_ of the coefficent       |
| **p.values**        | p value of the parameter          |
| **ci.low**          | Confidence interval: low value    |
| **ci.high**         | Confidence interval: high value   |
| **mean\_\<TARGET>** | The mean of the target            |

#### Model Extra - LR

| Key                 | Description                           |
| ------------------- | ------------------------------------- |
| **attribute**       | Attribute names                       |
| **TARGET\_NAME**    | Attribute Coefficient for each target |
| **mean\_\<TARGET>** | The mean of the target                |

#### Model Extra - Kmeans

| Key                           | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| **kmeans\_k**                 | K value for this KMeans model                                |
| **kmeans\_silhouette**        | Silhouette algorithm score                                   |
| **kmeans\_wssse**             | Within set sum of squared error, useful for elbow heuristic. |
| **kmeans\_silhouette\_trace** | All the values used to compute silhouette score.             |

#### Model Extra - RF

| Key                 | Description                                                                    |
| ------------------- | ------------------------------------------------------------------------------ |
| **num\_classes**    | Number of classes in the model.                                                |
| **num\_trees**      | Number of trees in the forest                                                  |
| **total\_nodes**    | Total number of Nodes                                                          |
| **always\_split**   | Whether this forest split node that contains all the same value                |
| **increment**       | Memory increment used when incrementing the underlying array storing the trees |
| **step**            | Gradient step.                                                                 |
| **mean\_\<TARGET>** | The mean of the target                                                         |

#### Model Extra - KM

| Key                       | Description                                                          |
| ------------------------- | -------------------------------------------------------------------- |
| **quantiles\_hazard**     | 25%,50%,75% Quantile Ranges for Hazard Function                      |
| **quantiles\_survival**   | 25%,50%,75% Quantile Ranges for Hazard Function                      |
| **hazard\_{GROUP\_ID}**   | DataFrame representing the Hazard Function for group **GROUP\_ID**   |
| **survival\_{GROUP\_ID}** | DataFrame representing the Survival Function for group **GROUP\_ID** |

#### Model Extra ECOD

| Key                          | Description                             |
| ---------------------------- | --------------------------------------- |
| **size**                     | Number of features                      |
| **ecod\_anomaly\_threshold** | Value of the threshold                  |
| **skewness**                 | Values of the skewness for each feature |
| **mean**                     | Values of the mean for each feature     |

#### Model Extra Principal Component Regression

| Key                    | Description                                                                                                                       |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **lambda**             | Value used to build model.                                                                                                        |
| **maxiter**            | Value used to build model.                                                                                                        |
| **tolerance**          | Value used to build model if applicable.                                                                                          |
| **max\_lambda\_power** | Value used to build model if applicable.                                                                                          |
| **min\_lambda\_power** | Value used to build model if applicable.                                                                                          |
| **num\_lambda**        | Value used to build model if applicable.                                                                                          |
| **aicc\_vec**          | if using **pcr\_type**=`full` and aicc=`true`, modelInfo will contain the vector of aicc values for each value in **lambda\_vec** |
| **lambda\_vec**        | if using **pcr\_type**=`full` and **aicc**=`true`, modelInfo will contain the vector of **lambda** values used to test.           |
| **q**                  | Value if q used if **pcr\_type**=`qfirst`                                                                                         |
| **model**              | This is the coefficients of the linear model aka mb.                                                                              |
| **model\_bias**        | This is the bias term of the linear model aka mb0.                                                                                |
| **standardize**        | Value used to build model if applicable.                                                                                          |
| **centerY**            | Value used to build model if applicable.                                                                                          |
| **muy**                | Mean of the target of the model.                                                                                                  |
| **mux**                | Mean of the features of the model.                                                                                                |
| **sig**                | Vector of standard deviation of the features used for this model.                                                                 |
| **Wtilde**             | If **standardize**=`true`, the pca projection matrix is contained in a matrix called that name.                                   |
| **W**                  | If **standardize**=`false`, the pca projection matrix is contained in a matrix called that name.                                  |

#### Model Extra Covariance Estimator

| Key                    | Description                                                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **estimate\_type**     | Value used to build model.                                                                                                    |
| **tuning\_type**       | Value used to build model if applicable.                                                                                      |
| **cxx\_est**           | The estimate of the covariance matrix                                                                                         |
| **ddof**               | Value used to build model.                                                                                                    |
| **theta**              | Graphical Lasso only. This is the inverse of the covariance matrix that is obtained.                                          |
| **B**                  | Graphical Lasso only. This is a matrix containing the coefficients of the underlying Lasso models solved for graphical lasso. |
| **lambda**             | Value used to build model if applicable.                                                                                      |
| **maxiter**            | Value used to build model if applicable.                                                                                      |
| **tolerance**          | Value used to build model if applicable.                                                                                      |
| **initialize\_gl**     | How the first iteration of graphical lasso was initialized.                                                                   |
| **gamma**              | Value used to build model if applicable.                                                                                      |
| **min\_lambda\_power** | Value used to build model if applicable.                                                                                      |
| **max\_lambda\_power** | Value used to build model if applicable.                                                                                      |
| **num\_lambda**        | Value used to build model if applicable.                                                                                      |
| **ebic\_vec**          | if **tuning** is `ebic,` vector with values of **ebic** at values of **lambda**                                               |
| **lambda\_vec**        | The array of values of lambda at which to build models is applicable                                                          |
| **alpha**              | Value used to build model if applicable.                                                                                      |
| **q**                  | Value used to build model if applicable.                                                                                      |

#### Model Extra Partial Least Square Regression

| Key             | Description                                                                               |
| --------------- | ----------------------------------------------------------------------------------------- |
| **model**       | This is the coefficients of the linear model aka mb                                       |
| **model\_bias** | This is the bias term of the linear model aka mb0                                         |
| **standardize** | Value used to build model if applicable.                                                  |
| **muy**         | Mean of the target of the model                                                           |
| **mux**         | Mean the features of the model                                                            |
| **sig**         | Standard deviation of the features of the model                                           |
| **ddof**        | Value used to build model.                                                                |
| **oneq**        | Set to `true` if you want a model for only one value of q. `false` if models for Q = 1..q |

## Get Code for Model

LDA and MLR models allow retrieval of their internal representation as a Java method or SQL function. This API function call can be used with these models to perform predictions. The result is a string that represents the definition of a JAVA method or SQL function.

### URL

```
GET /symetry/rest/{cid}/projects/{pid}/models/{modelid}/code?language=java
```

### Query Parameters

| Parameter    | Required/Optional? | Description                                                                                      |
| ------------ | ------------------ | ------------------------------------------------------------------------------------------------ |
| **language** | Required           | Targeted programming language for code. Choices are: - java = Java method. - sql = SQL function. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity | Example                                                                                                                                                                                                     |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **String**           | `/* Java Function */ + public double ldaRed_score( + double petal_length, + double petal_width, + double sepal_width + { + (-8.78 * petal_length) + + (-15.84 * petal_width) + + (2.626 * sepal_width) + }` |

### Sample Request/Response

```
Request:
GET url=http://charm:8080/symetry/rest/c1/projects/irisTest/models/model1/code?language=java

Response:
"{"statusCode":"OK","statusString":"OK","values":{"string":"/* Java Function */\npublic double ldaRed_score(\n\tdouble petal_length,\n\tdouble petal_width,\n\tdouble sepal_width,\n\tdouble petal_length_b1,\n\tdouble sepal_width_b1,\n\tdouble petal_width_b2,\n\tdouble sepal_length)\n{\n\treturn (-8.780458794255027 * petal_length) + \n\t\t(-15.841653689691597 * petal_width) + \n\t\t(2.6264699508510816 * sepal_width) + \n\t\t(-4.283122721819282 * petal_length_b1) + \n\t\t(-1.0747512015680236 * sepal_width_b1) + \n\t\t(58.97284497145444 * petal_width_b2) + \n\t\t(3.4278968694343033 * sepal_length);\n}\n"}}"
```

## Delete a Model

Delete a model from a project

### URL

```
DELETE /symetry/rest/{cid}/projects/{pid}/models/{modelid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |
