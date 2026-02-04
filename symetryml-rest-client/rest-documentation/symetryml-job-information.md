# SymetryML Job Information

## Specific Job Information

This API function reports specific job information.

### URL

```
GET /symetry/rest/{cid}/jobs/{jobid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                      |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------ |
| **200**          | OK                  | Job has finished. `{"statusCode":"OK","statusString":"Job is finished","values":{}}`             |
| **202**          | ACCEPTED            | Job is still running. `{"statusCode":"ACCEPTED","statusString":"Job still running","values":{}}` |
| 400              | BAD REQUEST         | Invalid job ID. `{"statusCode":"BAD_REQUEST","statusString":"No such job","values":{}}`          |

### HTTP Response Entity

| HTTP Response Entity                 | Description The entity returned by the job depends on the type of job that just finished. For example:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AutoSelectJobs**                   | The JobInfo will contains many additional information in the `details` map. All the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) parameters are returned as well as the id of the data source being used for validation dataset or the first 5 lines of the [Dataframe](appendix-a-json-data-structure-schema.md#dataframe-json) if an actual [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) was used instead of a data source id - depending on which REST method was used ([With data source id](auto-select-with-symetryml.md#auto-select-rest-api) or [with a Dataframe](auto-select-with-symetryml.md#auto-select-dataframe-rest-api)) |
| **Single ProjectInfo Jobs**          | See [Project Info](symetryml-projects-rest-api.md#symetryml-project-information-about-one-project) endpoint. It returns a [ProjectInfo](appendix-a-json-data-structure-schema.md#projectinfo) response upon completion.                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **List Projects Info Jobs**          | See [List All Projects Info ](symetryml-projects-rest-api.md#symetryml-project-information-about-all-projects)endpoint. It returns a [ProjectInfoList](appendix-a-json-data-structure-schema.md#projectinfolist) response upon completion.                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **DS Predict Jobs**                  | See [Predict API](prediction-api.md). It returns a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) that contains the predictions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ExploreJob**                       | return 2 entities [KSVDMap](appendix-a-json-data-structure-schema.md#ksvdmap) entity and a [ProjectInfo](appendix-a-json-data-structure-schema.md#projectinfo) entity. Please consult the Exploration HTTP Response Entity for details                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **SVDJob**                           | returns a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **PCA Job**                          | return 2 entities (pcaVectors and pcaValues) Both are [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) data structure.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **AwsInfoJob**                       | returns a [AwsInfo](appendix-a-json-data-structure-schema.md#awsinfo) datastructure                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **DSUpdateJob**                      | returns [ProjectInfo](appendix-a-json-data-structure-schema.md#projectinfo) that reflects new state of the project.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **SparkDSUpdateJob**                 | returns [ProjectInfo](appendix-a-json-data-structure-schema.md#projectinfo) that reflects new state of the project.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **ExploreVIFJob**                    | returns a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Optimized Kmeans Job**             | returns a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap). The returned keys are in the format `kmeans_{N}_wssse` and `kmeans_{N}_silhouette` where the corresponding values represent the respective WSSSE and Silhouette scores for a model build with `N` clusters.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **DSPredictDataFrameJob**            | returns a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json). The returned DataFrame contains the following information: **id** - row id **res** - anomaly flag via a simple rule **q95** - 95% quantile of model scores **q98** - 98% quantile of model scores **q99** - 99% quantile of model scores **evt** - anomaly flag via Extreme Value Theory rule                                                                                                                                                                                                                                                                                                             |
| Other jobs do not return any entity. | If a job is not finished or has yet to be started, it returns a JobInfo entity that contains detailed information about a given job. See [JobInfo](appendix-a-json-data-structure-schema.md#jobinfo).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## List All Jobs

This API function lists all current jobs.

### URL

```
GET /symetry/rest/{cid}/jobs
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                          |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------ |
| **200**          | OK                  | Job has finished. `{"statusCode":"OK","statusString":"Job is finished","values":{}}` |

### HTTP Response Entity

| HTTP Response Entity                                                    | Description                                                            |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| [**JobInfo**](appendix-a-json-data-structure-schema.md#jobinfo) **\[]** | Response contains a list of all the currently created on the computer. |

## Kill a Job

This API function kills a job.

### URL

```
DELETE /symetry/rest/{cid}/jobs/{jobid}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                             |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------- |
| **200**          | OK                  | Job was killed.                                                                         |
| **202**          | ACCEPTED            | Job cannot be killed.                                                                   |
| **400**          | BAD REQUEST         | Invalid job ID. `{"statusCode":"BAD_REQUEST","statusString":"No such job","values":{}}` |
