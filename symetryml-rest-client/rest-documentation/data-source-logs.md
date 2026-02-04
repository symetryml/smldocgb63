# Project Data Source Logs

This functionality allows to get report metrics on the various data sources that were processed by a SymetryML project. Data Sources logs can be requested as well as the logs about the SymetryML streaming API [Learn](symetryml-projects-rest-api.md#learn-new-data-incremental-update) and [Forget](symetryml-projects-rest-api.md#forgetting-data-decremental-update).

## Data Source Log Information

The data source log contains information about each data source that was processed (learn / forget) by a SymetryML project. It also contains other _meta information_ about each invocation:

### Data Source Log Definitions

| Key                             | Description                             |
| ------------------------------- | --------------------------------------- |
| **client\_id**                  | Client id who made the call             |
| **ds\_client\_ip**              | Ip address of the client                |
| **ds\_request\_type**           | Learn or Forget                         |
| **ds\_name**                    | Data Source name                        |
| **ds\_request\_datetime**       | Date of the request                     |
| **ds\_request\_df\_numrows**    | Number of rows processed                |
| **ds\_request\_process\_time**  | Process time                            |
| **ds\_request\_response\_code** | HTTP Response code (E.g. 200, 4xx, etc) |

## Real Streaming, AKA Learn / Forget Log Information

The Learn / Forget Stream log is a bit different. As these REST endpoints are designed to continuously stream data into a project, they can easily grow large if we keep track of meta-data for each invocation that pushes new data into a SymetryML project. For more information on the SymetryML streaming API consult the following sections:

* [Real Time Streaming Data](symetryml-projects-rest-api.md#real-time-streaming-of-data-into-symetryml-projects)
  * [Incremental Learning](symetryml-projects-rest-api.md#learn-new-data-incremental-update)
  * [Decremental Forget](symetryml-projects-rest-api.md#forgetting-data-decremental-update)

The learn / forget logging REST endpoint aggregate data based on the type of data that was pushed into the learn / forget streaming API for a given project. More particularly the following fields - or _primary keys_ - are used to aggregate logging information:

* `client_id`
* `client_request_type`
* `client_request_reponse_code`
* `client_request_df_attributes_names`
* `client_request_df_attributes_types`

### Learn / Forget Log Definitions

The following table lists what information is returned for the learn / forget logging rest API based on the previous list. The _primary keys_ are in listed in **bold**.

| Key                                                 | Description                                                                      |
| --------------------------------------------------- | -------------------------------------------------------------------------------- |
| **client\_id**                                      | Client id who made the call                                                      |
| **client\_request\_type**                           | Learn or forget                                                                  |
| **client\_request\_reponse\_code**                  | HTTP Response code (e.g. 200, 4xx, etc)                                          |
| **client\_request\_df\_attributes\_names**          | DataFrame attributes names                                                       |
| **client\_request\_df\_attributes\_types**          | DataFrame attributes types                                                       |
| **client\_request\_process\_time**                  | Total process time across all invocations                                        |
| **client\_request\_count**                          | Number of times such Dataframe were pushed via the learn or forget rest endpoint |
| **client\_request\_df\_attributes\_numrows\_total** | Total number of rows processed so far                                            |

## Data Source Logs Rest API

### Data Source Get Log

Get a given project data source log. This log contains information about the data sources that were processed by a given project.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/getDsLog
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

| HTTP Response Entity                                                  | Example                                                                                                                                                            |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | Each string represents a Map containing the key value information described in the [Data Source Log Definitions](data-source-logs.md#data-source-log-definitions). |

#### Sample Request/Response Fed Create

Example response with project with two data sources successfully learned:

```
GET url="http://charm:8080/symetry/rest/c1/projects/p1/getDsLog"

Response:
{"statusCode":"OK","statusString":"OK","values":
        {"stringList":{
                "values":
                ["{"ds_request_datetime":"1605629571980","ds_request_response_code":"200","ds_name":"Iris_category_csv","ds_request_type":"learn","ds_client_id":"web-api","ds_request_process_time":"266","ds_client_ip":"172_16_104_1","ds_request_df_numrows":"150"}",
                "{"ds_request_datetime":"1605629597958","ds_request_response_code":"200","ds_name":"Iris_rtlm2_csv","ds_request_type":"learn","ds_client_id":"web-api","ds_request_process_time":"257","ds_client_ip":"172_16_104_1","ds_request_df_numrows":"150"}"]
                }
        }
}
```

### SymetryML Project Learn / Forget Logs

The REST endpoint provides logging information about data that was pushed to a given project via the various stream APIs.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/getLFStats
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

| HTTP Response Entity                                                  | Example                                                                                                                                                               |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | Each string represent a Map containing the key value information described in the [Learn / Forget Log Definitions](data-source-logs.md#learn-forget-log-definitions). |

#### Sample Request/Response

```
GET url="http://charm:8080/symetry/rest/c1/projects/p1/getLFStats"

Response:
{"statusCode":"OK","statusString":"OK","values":
        {"stringList":{
                "values":
                ["{"client_request_df_attributes_numrows_total":"506","client_request_type":"learn","client_request_df_attributes_names":"CRIM,ZN,INDUS,CHAS,NOX,RM,AGE,DIS,RAD,TAX,PTRATIO,B,LSTAT,MEDV","client_request_reponse_code":"200","client_request_count":"1","client_request_df_attributes_types":"C,C,C,B,C,C,C,C,C,C,C,C,C,C","client_id":"default-rest-client","client_request_process_time":"301"}"]
                }
        }
}
```
