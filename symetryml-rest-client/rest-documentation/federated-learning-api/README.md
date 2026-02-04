# Federated Learning API

With SymetryML Federated Learning can be organized in a centralized manner, where only the administrator has access to information sharing and model building, or a decentralized framework where all peers in the federation have access to information sharing and model building. There is also the option to create a hybrid Federated Learning network which can be a combination of centralized and decentralized federated learning networks.

Creating a federation starts with a single node, the Administrator of the Federation. The administrator is responsible for creating the federation, inviting peers to join the federation, and determining how often the federation will share information, otherwise known as the “sync” schedule or frequency of sharing information.

For more details on the various topologies available when using federated learning with SymetryML please consult[ Federated Learning Topologies](about-federated-learning-topologies.md) documentation.

## Federated Learning: Create Federation

Please consult the following for details on how to create a federation depending on if you are using \[NATS] (https://www.nats.io) or [AWS](https://aws.amazon.com/) backed federation.

* AWS : [Consult this section](federated-learning-with-aws-1.md#create-aws-backed-federation)
* NATS : [Consult this section](federated-learning-with-nats-1.md#create-nats-backed-federation)

## Federated Learning: Get Federation Info

This rest call returns the information about the federation. The information is returned encrypted with the user secret key, that is the SymetryML user key.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/info
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

### HTTP Response Entity

| HTTP Response Entity                                                                           | Description            |
| ---------------------------------------------------------------------------------------------- | ---------------------- |
| **Encrypted** [**FederationInfo**](../appendix-a-json-data-structure-schema.md#federationinfo) | See the example below. |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/fedml/nh_test_admin/info"

Response:
{"statusCode":"OK","statusString":"OK","values":{"fedInfoEncrypted":"mD3yV8TamG8BsXx/kGGqtnXm+O6njUfyKnH5U86fldQ7Lbsp0t6V5rAM7zhqtMaQsHPodR/5ATf5ovKTlnUSNkWQKkrbaQDR+rHnOymTcyfahXihXoccar67oE+VVYGJDQpDB7IOEVnKKzdFWqBxlHzLzGip0vFGcnfTXR3689oVB5BDbOSvsGkxfjRMoZBJLLVskN4yqmq+kbP5lrP+zuhR5NpTKCrPIe9phIiWlURzHb2RxsI6LHGhySz9iKytesYD4Kdl8iOzfffdx/jPDewreaks2Cx5RFobHPxfuBHYWHzLn2DISlxO+Pemg3Rq0FCZ7Hk9Fexps04yTxkk7S9iBDEWDufCK4cRIEYq1/feytV/Olv2a+OZDlpG38aHyk6uiYzpx22R8DJLoTjBW8Cp/JFgokyYdx29bM2MqlsfUSxIEulbfjoymdtu95PT4YjUelogTctAn9+/xnWiyrET3Fp9VqkCFnXuIdCm5udWspqr7sconMe1rgss5AMPbwqEAcabJR8rGl6zNQX6HgUOaIbKF7PpETMJvlLOby12uY0iljykqaZQNOTd1rjVmDXxxDlC7GcLinPpJE+WOGevGfrvcM71uIbDB1+a7Rz5QKvtW5X9RgL1rZtodXqiwpT1Br+THaKcrBnD45fG7EHBOwPNR4ue+uuvMi+57xLi1ntUIbPBJ7pzRZ09tkUCfh+aQvAbSoThna5Ef6XmTQ\u003d\u003d"}}
```

## Federated Learning: Get Encrypted Federation Info

This Rest endpoint is used in order to get 'an invite' to join the federation by the administrator of the federation. The body of the request contain the password to use to encrypt the resulting `encryptedGroupInfo` can then be shared with another peer so that they can join. Please note that `encryptedGroupInfo` can only be used 1 time. For each peer that want to join the federation a unique `encryptedGroupInfo` needs to be generated.

### URL

```
POST /symetry/rest/{cid}/fedml/{pid}/getEncrypted
```

### HTTP Response Entity Key.

| HTTP Response Entity   | Description                                                                                                                         |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **encryptedGroupInfo** | Contains the information necessary for a peer to join a federation encrypted with the password provided in the body of the request. |

| Mandatory key       | Required / Optional | Value Description                                     |
| ------------------- | ------------------- | ----------------------------------------------------- |
| **passwd**          | Required            | Password to use to encrypt the federation information |
| **fed\_rest\_host** | Required            | Hostname where admin can be reach at.                 |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **400**          | BAD REQUEST         | SymetryML project does not exist. `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

### HTTP Response Entity

The response will be stored in the `encryptedGroupInfo` entity which is a string. That string is a json encoded string encrypted with the provided password. Once decrypted you get a \<string, string> map that contains some of the following key: The response token `fed_req_token` can only be used one time.

* sync\_sched
* fed\_name
* fed\_secret\_key
* fed\_req\_token
* for AWS based federation:
  * aws\_topic\_arn
  * aws\_topic\_region
* for Nats based federation:
  * nats\_hosts
  * nats\_sec\_user
  * nats\_sec\_passwd
  * nats\_sec\_token
  * nats\_sec\_nkey\_seed

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/fedml/nh_test_admin/getEncrypted"

Request Body Before Encryption:
{"passwd":"passwd","fed_rest_host":"http://charm:8080"}

Request Body:
qvh4P1y937flTE/R6GgtrN8Weu28/jCx14zBIG5zk4CEqC4+rLL/HtVvKqUthPsSVZN8BgWP39O+MKEyOsUo8w==

Response:
{"statusCode":"OK","statusString":"OK","values":{"fedInfoEncrypted":"ZwsCbdhP2GxfOgDBwJHoy61uRMZLNg7D581Cs9zWAgjwWpK2wScvgpTexpsVyilbvGThFVsWXz57n9BXY+DA1EmI+L29H+K60b1lI4v/WLr45HHCdMEDCcBAL+F3/3NcRBl5RcfgBlC4RTV6+h/75rr21lgMGxnVXlYBoUh//JpBxfJhEZyxa1gv0WVdCXDDT/M3Aq819r+6rS89jj38SYj/Hzj7pJaEHvbxe+OniH5Z90xEortLZeVdG8HE5zzqLegKpChFYFE4l60RXtdyE/fa7uY3gM7Q/yU+8SQ3Yjkvl8B5Gs7coBdgkcKNmcgiBqG4YrQSg8SCuX6RsKs4B4qV61w+AZkmt9+ipp049Hpxwu0myZ2agomcAMKdZHDv"}}
```

### Decrypted Message Example:

Once decrypted the Federation info will contains information like in the following example. This is basically a map with a few key/ value pair. These key value pairs must be used when joining a federation.

```
Decrypted JSON:
{
    "sync_sched":"m1",
    "fed_rest_url":"http://charm:8080/symetry/rest/c1/fedml/nh_test_admin/createPolicies",
    "fed_name":"nh_test_doc",
    "fed_secret_key":"t6Zat/ZAJyYBtmVXATUDCQ\u003d\u003d",
    "aws_topic_arn":"arn:aws:sns:us-east-1:428117700962:nh_test_doc",
    "aws_topic_region":"US_EAST_1",
    "fed_req_token":"FEDTOKEN38f45fe16545e80c",
}
```

## Federated Learning: Join a Federation

Please consult the following sections for details on how to join an existing federation depending on if you are using Nats (www.nats.io) or AWS backed federation.

* AWS : [Consult this section](federated-learning-with-aws-1.md#join-an-aws-backed-federation)
* NATS : [Consult this section](federated-learning-with-nats-1.md#join-a-nats-backed-federation)

## Federated Learning: Start Pulse

Instruct the federated project to start syncing with other nodes in the federation.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/startPulse
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Federated Learning: Change Schedule

This rest endpoint can only be executed by the Federation Administrator. It enables a change of the synchronization schedule - that is, how often peers in the federation synchronize with other peers.

The [ProjectInfo](../appendix-a-json-data-structure-schema.md#projectinfo) returned by the [Project Info](../symetryml-projects-rest-api.md#symetryml-project-information-about-one-project) rest call contains information about whether or not a project is the federation administrator. This information is present in the `param` map field under the `fed_is_admin` key.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/newSchedule
```

### Create Project Query Parameters

| Parameter       | Required / Optional | Description                             |
| --------------- | ------------------- | --------------------------------------- |
| **newSchedule** | Required            | New schedule for the federation syncing |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Federated Learning: Stop Pulse

Instruct the federation project to stop syncing with other nodes in the federation.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/stopPulse
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Federated Learning: Manual Sync

Send a single synchronization request message to the federation. This is used when a given peer/client wants to receive synchronization info from the other peers in the federation.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/fullSyncRequest
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Allows Peer Explore for a Given Node

Send a single synchronization request message to the federation. This is used when a given peer/client wants to receive synchronization info from the other peers in the federation.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/setExplorePeers
```

### Query Parameters

| Parameter    | Required / Optional | Description                             |
| ------------ | ------------------- | --------------------------------------- |
| allowExplore | Required            | Whether to allow or not (true or false) |



### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Validate PSR Contract

Allows to Validate a PSR Contract. See the [PSR Contract section](../about-federated-learning.md#psr-contracts) for additional information. The body of the request is a [StringList](../appendix-a-json-data-structure-schema.md#stringlist) which contains a list of business rule to be enforced.

### URL

```
POST /symetry/rest/{cid}/fedml/{pid}/psrContractValidate [body=StringList]
```

### HTTP Responses

<table><thead><tr><th width="205">HTTP Status Code</th><th width="223">HTTP Status Message</th><th>Description</th></tr></thead><tbody><tr><td><strong>200</strong></td><td>OK</td><td>Success.</td></tr></tbody></table>

## Update PSR Contract

Update the PSR Contract in a federation. Only the federation admin can invoke this REST endpoint. See the [PSR Contract section](../about-federated-learning.md#psr-contracts) for additional information. The body of the request is a [StringList](../appendix-a-json-data-structure-schema.md#stringlist) which contains a list of business rule to be enforced. Please note that the PSR Contract is not validated, so care must be taken to invoke [Validate PSR Contract ](./#validate-psr-contract)first.

### URL

```
POST /symetry/rest/{cid}/fedml/{pid}/addPsrContract [body=StringList]
```

### HTTP Responses

<table><thead><tr><th width="205">HTTP Status Code</th><th width="223">HTTP Status Message</th><th>Description</th></tr></thead><tbody><tr><td><strong>200</strong></td><td>OK</td><td>Success.</td></tr></tbody></table>

###

### HTTP Response Entity

The response is a String entity. If the PSR Contract is not valid, It will contains a description of the error in the PSR contract&#x20;

## Federated Learning: Get Error Log

Return error logging messages. For federated project many _things_ can happen in the background some of which that may cause errors, invalid AWS credentials, invalid S3 bucket path for example. This rest endpoint enables the checking for various error that might occur asynchronously in the background.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/getErrorLog
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                     | Example                                                              |
| ------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| [**StringList**](../appendix-a-json-data-structure-schema.md#stringlist) | see [Get Error Log](./#sample-request-response-fed-create-1) example |

### Sample Request/Response Fed Create

```
GET url="http://charm:8080/symetry/rest/c1/fedml/kkg-node1/getErrorLog"

Response:
TBD
```

## Clear Federation Error Log

Clear the federation error log for this node.

### URL

```
DELETE /symetry/rest/{cid}/fedml/{pid}/clearErrorLog
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| 200              | OK                  | Success     |

## Get Sync Log

Return synchronization logging messages.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/getSyncLog
```

### HTTP Responses

<table><thead><tr><th width="394.3333333333333">HTTP Status Code</th><th>HTTP Status Message</th><th>Description</th></tr></thead><tbody><tr><td><strong>200</strong></td><td>OK</td><td>Success.</td></tr></tbody></table>

### HTTP Response Entity

| HTTP Response Entity                                                     | Example                                                                         |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| [**StringList**](../appendix-a-json-data-structure-schema.md#stringlist) | see [Get Sync Log](./#sample-request-response-fed-create-bad-s3-bucket) example |

### Sample Request/Response Fed Create - Bad S3 bucket

```
GET url="http://charm:8080/symetry/rest/c1/fedml/kkg-node1/getErrorLog"

Response:
{"statusCode":"OK","statusString":"OK","values":{"stringList":["com.amazonaws.services.s3.model.AmazonS3Exception: Access Denied (Service: Amazon S3; Status Code: 403; Error Code: AccessDenied; Request ID: BECB121F96D0DA27; S3 Extended Request ID: iSi3fe/l6B8pTtVx4dovZ4OM/5PwDRRbEmpzoZaylnL3SuFibSrnMY1LkwMFpjUaO6LouBJEu1g\u003d), S3 Extended Request ID: iSi3fe/l6B8pTtVx4dovZ4OM/5PwDRRbEmpzoZaylnL3SuFibSrnMY1LkwMFpjUaO6LouBJEu1g\u003d"]}}
```

## Get Federation Sync Statistics

Return information about status of the various synchronization. This is specially useful when using [PSR Contract ](../about-federated-learning.md#psr-contracts)and you want to know if other peers in the federation have PSR contract failure and how many. Please see the [FedSyncStats](../appendix-a-json-data-structure-schema.md#fedsyncstats) data structure for details.

### URL

```
GET /symetry/rest/{cid}/fedml/{pid}/syncStats
```

### HTTP Responses

<table><thead><tr><th width="394.3333333333333">HTTP Status Code</th><th>HTTP Status Message</th><th>Description</th></tr></thead><tbody><tr><td><strong>200</strong></td><td>OK</td><td>Success.</td></tr></tbody></table>

### HTTP Response Entity

| HTTP Response Entity                                                     | Example |
| ------------------------------------------------------------------------ | ------- |
| [FedSyncStats](../appendix-a-json-data-structure-schema.md#fedsyncstats) | TBD     |

## Get AWS Info

This rest endpoint is provided so that one can inspect the AWS resources utilized by the federation.

### URL

```
POST /symetry/rest/{cid}/fedml/{pid}/awsinfo [body=Encrypted Map]
```

### Request Body Map Keys

| Key                  | Description                                                         |
| -------------------- | ------------------------------------------------------------------- |
| **aws\_access\_key** | Required                                                            |
| **aws\_secret\_key** | Required                                                            |
| **aws\_region**      | Optional, if not passed, result will be returned for all aws region |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| 200              | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                               | Example                                                                                                                                                                                 |
| ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**AwsInfo**](../appendix-a-json-data-structure-schema.md#awsinfo) | Please consult [AwsInfo](../appendix-a-json-data-structure-schema.md#awsinfo) for an example. The response will be an encrypted JSON string. The entity key will be `awsInfoEncrypted`. |

### Sample Request/Response

```
Request
POST url="http://charm:8080/symetry/rest/c1/fedml/awsinfo"

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4
```
