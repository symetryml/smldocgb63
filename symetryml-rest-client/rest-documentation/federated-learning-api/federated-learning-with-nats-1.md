---
description: >-
  SymetryML can use NATS (www.nats.io) as a backend queueing system to power a
  federation. The REST calls to create / join a federation
---

# Federated Learning with Nats

## Create Nats Backed Federation

### URL

```
POST /symetry/rest/{cid}/fedml/create?pid={new-project-id} [body=Encrypted Map]
```

This REST endpoint allows the user to create a new federation. The user who creates the federation also becomes its administrator. The body is a map that is encoded to a JSON string and then encrypted using the user secret key.&#x20;

#### Federation Key Map Value

| Key                           | Required / Optional | Value Description                                                                                                                                                                                                            |
| ----------------------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **fed\_name**                 | Required            | The name of the federation.                                                                                                                                                                                                  |
| **fed\_local\_type**          | Optional            | The type of project to use. Supported values: `cpu` and `gpu`. The default value is `cpu`.                                                                                                                                   |
| **sync\_sched**               | Required            | The sync schedule, consult [FedML Terminology](../about-federated-learning.md#federation-terminology) for more details.                                                                                                      |
| **nats\_hosts**               | Required            | Urls of hosts                                                                                                                                                                                                                |
| **fed\_encrypt**              | Optional            | Whether to encrypt or not the sync message. The default is false.                                                                                                                                                            |
| **nats\_sec\_user**           | Optional            | When using nats user/password authentication, this allows to specify the user. [https://docs.nats.io/developing-with-nats/security	](https://docs.nats.io/developing-with-nats/security)for more details.                    |
| **nats\_sec\_passwd**         | Optional            | When using nats user/password authentication, this allows to specify the user password. [https://docs.nats.io/developing-with-nats/security	](https://docs.nats.io/developing-with-nats/security)for more details.           |
| **nats\_sec\_token**          | Optional            | When using nats token authentication, this allows to specify the token to use for authentication. [https://docs.nats.io/developing-with-nats/security	](https://docs.nats.io/developing-with-nats/security)for more details. |
| **nats\_sec\_nkey\_seed**     | Optional            | When using nats NKEY authentication, this allows to specify the seed of the NKEY used. This is set by the federation administrator and will be shared with other peers when they join the federation.                        |
| **nats\_req\_timeout**        | Optional            | NATS request timeout in seconds, default is 30.                                                                                                                                                                              |
| **nats\_stream\_max\_memory** | Optional            | NATS maximum memory used by underlying stream managing project sharing with other peers. The default is (1Gig, 1024 x 1024 x 1024 bytes).                                                                                    |

### Query Parameters

| Parameter   | Required / Optional | Description                           |
| ----------- | ------------------- | ------------------------------------- |
| **pid**     | Required            | Name of the new SymetryML project.    |
| **persist** | Optional            | Whether to persist or not the project |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                       |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **201**          | CREATED             | Success.  `{"statusCode":"CREATED","statusString":" + SYMETRYML Created with id:r1","values":{}}`                                                 |
| **409**          | CONFLICT            | SymetryML project already exists.  `{"statusCode":"CONFLICT","statusString":" + Customer [c1] already have SYMETRYML with id[r1], ","values":{}}` |

### HTTP Response Entity

None

### Sample Request/Response Fed Create

```
POST url="http://charm:8080/symetry/rest/c1/fedml/create?pid=nh_test_admin

Request Body:

KjxfT4/2TpJsfwd+gAG0zAnH7r+fs7IyKrlMXNoqh5CNQjoLioBW6HjXH5hRVqEYASueMI++fmMDAEOBIQD/e/3HDeA/SVojcmzywmxcMNotH9iTpboU9KIJZVq1JW3bxAv+e2COEuI+82qrQVJ4QifuAy5TxR+B5H0GjC0x4qiDK5h+TpEDnFB1Cjdv3xQyiDW8EetZNlCcriEdkTSvxI8duhdJIhFy4UYL1XzN7SavwmE/rddP+ETfeMmLJehseBOJr19xPiMR71maM2DybESQvCN1bWUj2XEvujasrcMMUK4+NEeC0pDe+gWoJhRgAZlRnOWgdwCKeBqcmqOc9Q==

Response:

{"statusCode":"CREATED","statusString":"Project Created with id:nh_test_admin","values":{}}
```

## Federated Learning Get Encrypted Federation Info

### URL

```
POST /symetry/rest/{cid}/fedml/{pid}/getEncrypted
```

**Get Encrypted Body Key Map Value.**

TDB new ResponseEntityKey.

| Mandatory key       | Required / Optional | Value Description                                     |
| ------------------- | ------------------- | ----------------------------------------------------- |
| **passwd**          | Required            | Password to use to encrypt the federation information |
| **fed\_rest\_host** | Required            | Hostname where admin can be reach at.                 |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                     |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                        |
| **400**          | BAD REQUEST         | SymetryML project does not exist.  `{"statusCode":"BAD_REQUEST","statusString":"Cannot Find SYMETRYML id[r5] for Customer id [c1]","values":{}` |

### HTTP Response Entity

The response will be stored in the `fedInfoEncrypted` entity which is a string. That string is in turn a json encoded string encrypted with the provided password. Once decrypted you get a \<string, string> map that contains the following key: (Consult [Federation Key Map](./#federation-key-map-value) for details). The response token `fed_req_token` can only be used one time.

* sync\_sched
* fed\_name
* fed\_secret\_key
* nats\_hosts
* fed\_req\_token

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

Once decrypted the Federation info will contains information like in the following example. This is basically a map with a few key/ value pair. These key value pairs must be used when joining a federation. See [section](./#mandatory-key-value-pairs-from-federation-admin) for details.

```
Decrypted JSON:
{
    "sync_sched":"m1",
    "fed_rest_url":"http://charm:8080/symetry/rest/c1/fedml/nh_test_admin/createPolicies",
    "fed_name":"nh_test_doc",
    "nats_hosts":"nats://localhost:4222",
    "fed_req_token":"FEDTOKEN38f45fe16545e80c",
}
```

## Join a Nats Backed Federation

### URL

```
POST /symetry/rest/{cid}/fedml/createJoin?pid={new-project-id} [body=Encrypted Map]
```

This REST endpoint allows the user to join an existing federation. The body is a map that is encoded to a JSON string and then encrypted using the user secret key. This encryption is necessary in order to protect sensitive information like _aws access key_ and _aws secret key_ that are required to be passed as part of the body.

### Federation Join Request Body

The request body is composed of 2 types of key/value pairs:

* Those that come from the Admin in an encrypted form. See [Decrypted Message](./#decrypted-message-example) example.
  * [Mandatory Keys](./#mandatory-key-value-pairs-from-federation-admin)
* Those that relates to the new node to be created.
  * [New Node Mandatory Keys](./#request-body-key-value-pairs)

#### Mandatory Key / Value Pairs from Federation Admin

The following key value will be received as part of the password encrypted json message from the federation admin. They need to be added the the query body without modification.

<table data-header-hidden><thead><tr><th width="215.6225005677133">Key</th><th></th><th></th></tr></thead><tbody><tr><td>Key</td><td>Mandatory / Optional</td><td>Description</td></tr><tr><td><strong>sync_sched</strong></td><td>Mandatory</td><td>The sync schedule, consult <a href="../about-federated-learning.md#federation-terminology">FedML Terminology</a> for more details.</td></tr><tr><td><strong>fed_secret_key</strong></td><td>Mandatory</td><td>The secret key used to encrypt info exchanged between nodes in a federation.</td></tr><tr><td><strong>fed_name</strong></td><td>Mandatory</td><td>The federation id.</td></tr><tr><td><strong>fed_req_token</strong></td><td>Mandatory</td><td>The token used to verify that a join request has not expired.</td></tr><tr><td><strong>nats_hosts</strong></td><td>Mandatory</td><td>The URL of the NATS service.</td></tr><tr><td><strong>nats_sec_user</strong></td><td>Optional</td><td>The user for NATS user/password communication security. Note: this is mandatory if <strong>nats_sec_token</strong> is not used.</td></tr><tr><td><strong>nats_sec_passwd</strong></td><td>Optional</td><td>The password for NATS user/password communication security. Note: this is mandatory if <strong>nats_sec_token</strong> is not used.</td></tr><tr><td><strong>nats_sec_token</strong></td><td>Optional</td><td>The token for NATS token communication security. Note: this is mandatory if <strong>nats_sec_user</strong> and <strong>nats_sec_passwd</strong> are not used.</td></tr></tbody></table>

#### Request Body Key / Value Pairs

Additional key / value pairs are needed in the request body

| Mandatory key        | Required / Optional | Value Description                                                                          |
| -------------------- | ------------------- | ------------------------------------------------------------------------------------------ |
| **fed\_local\_type** | Optional            | The type of project to use. Supported values: `cpu` and `gpu`. The default value is `cpu`. |

### Query Parameters

| Parameter   | Required / Optional | Description                                              |
| ----------- | ------------------- | -------------------------------------------------------- |
| **persist** | Optional            | Whether or not to persist the project. Defaults to true. |
| **pid**     | Optional            | Project name                                             |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                       |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **201**          | CREATED             | Success.  `{"statusCode":"CREATED","statusString":" + Fed Project created with join","values":{}}`                                                |
| **409**          | CONFLICT            | SymetryML project already exists.  `{"statusCode":"CONFLICT","statusString":" + Customer [c1] already have SYMETRYML with id[r1], ","values":{}}` |

### HTTP Response Entity

None

### Sample Request/Response Fed Create Join

```
POST url="http://charm:8080/symetry/rest/c1/fedml/createJoin?pid=node1"

Request Body:
KjxfT4/2TpJsfwd+gAG0zAnH7r+fs7IyKrlMXNoqh5CNQjoLioBW6HjXH5hRVqEYASueMI++fmMDAEOBIQD/e/3HDeA/SVojcmzywmxcMNotH9iTpboU9KIJZVq1JW3bxAv+e2COEuI+82qrQVJ4QifuAy5TxR+B5H0GjC0x4qiDK5h+TpEDnFB1Cjdv3xQycXSud7eRwy72gtY+wi8Rey3Ku1BerIRbhRVTBaHicb2duiZTZivdx1VJKA7BbH2s...


Response:
{"statusCode":"CREATED","statusString":"Fed Project created with join","values":{}}
```
