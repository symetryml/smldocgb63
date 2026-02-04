# Miscellaneous API

## Enable Histogram

### URL

```
POST /{cid}/projects/{pid}/histogramEnable?enable=[true|false]
```

### Query Parameters

| Parameter  | Required / Optional | Description                                              |
| ---------- | ------------------- | -------------------------------------------------------- |
| **enable** | Required            | Whether to enable or disable histogram for this project. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                                   |
| **400**          | BAD REQUEST         | User does not have the SymetryML project.  `{"statusCode":"BAD_REQUEST","statusString":" + Customer[c1] does not have Project with id[r1], ","values":{}}` |
