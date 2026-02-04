# Encoder Object REST API

## Create New Encoder Object

This API function call creates a new Encoder object.

### URL

```
POST /symetry/rest/{cid}/encoders/create?encodername={name}&encoderTarget={target} [body = KSVSMap]
```

### Create Encoder Body

It’s possible to specify parameters when creating an encoder. These parameters are passed in a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap) data structure. The following table list the possible key value pair that can be put in that map:

| Key             | Value                                                                                                                                                                                                                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **minTrimSize** | Set the number of positive examples for a key value to be kept in an encoder when performing trimming. That is if `minTrimSize` is 3 and a given key value was seen 25 time with 2 positive example, it will be trimmed. Note that this parameter only applies to Encoder with a binary target. |

### Query Parameters

| Parameter         | Required/Optional | Description                                                                                      |
| ----------------- | ----------------- | ------------------------------------------------------------------------------------------------ |
| **encoderName**   | Required          | Name of the new Encoder.                                                                         |
| **encoderTarget** | Required          | Target attribute to use.                                                                         |
| **targetType**    | optional          | Target Type, valid values are _C_ for continuous based encoder and _B_ for binary based encoder. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                               |
| ---------------- | ------------------- | ----------------------------------------- |
| **201**          | CREATED             | Success                                   |
| **409**          | CONFLICT            | An encoder with same name already exists. |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
GET url=http://charm:8080/symetry/rest/c1/encoders/create?encodername=enctest&targetName=clicked

Response:
{"statusCode":"CREATED","statusString":"Encoder[enctest] with Target[clicked] created for Customer[c1]","values":{}}
```

## Update an Encoder

This API function call updates an existing Encoder.

### URL

```
POST /symetry/rest/{cid}/encoders/{encodername}/learn [body = DataFrame]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

None

### Sample Request/Response

To be concise, we replaced some of the content of the body with (…) in the following example:

```
Request:
POST url=http://charm:8080/symetry/rest/c1/encoders/enctest/learn

Body:
{"attributeNames":["(...)"],"data":[["(...)]],"attributeTypes":["S","C","C","L","S","L","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","L","S","S","S","S","S","S","B","S","S","S","B","C","C","B","S","B","C","C","B","C","B"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{}}
```

## Delete an Encoder

This API function call deletes an existing Encoder.

### URL

```
DELETE /symetry/rest/{cid}/encoders/{encodername}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                   |
| ---------------- | ------------------- | --------------------------------------------- |
| **200**          | OK                  | Success                                       |
| **409**          | CONFLICT            | Encoder is being used by a SymetryML project. |

### HTTP Response Entity

None

## Encoder Statistics

This API function returns information about the key present in an Encoder.

### URL

```
GET /symetry/rest/{cid}/encoders/{encodername}/stats
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

| HTTP Response Entity | Description                                                                                                                                                                                                                           |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **DataFrame**        | For each attribute (e.g., key) in this Encoder, the following columns are returned in the [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json):  \* Key  \* Type  \* Count = number of different values for this key. |

### Sample Request/Response

```
Request:
GET url=http://charm:8080/symetry/rest/c1/encoders/enctest/stats

Response:
{"statusCode":"OK","statusString":"OK","values":{"dataframe":{"attributeNames":["key","type","count","mean","stddev","variance"],"data":[["adexchange","S","1.0","34452.0","0.0","0.0"],["badv_count","B","0.0","0.0","0.0","0.0"],["bcat_count","C","0.0","0.0","0.0","0.0"],["bid_price","C","0.0","0.0","0.0","0.0"],["campaign","S","16.0","2153.25","5851.762725880126","3.4243127E7"],["category","S","18.0","1914.0","3205.867011296265","1.0277583294117646E7"],["category_count","B","0.0","0.0","0.0","0.0"], (…) ]}}}
```

## Encoder Specific Key Values

This API function lets you obtain detailed information about all the values for a given key.

### URL

```
GET /symetry/rest/{cid}/encoders/{encodername}/keyvals/{key}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

| HTTP Response Entity | Description                                                                                                      |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **DataFrame**        | (see [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) )  \* Value  \* Type  \* Count  \* Sum |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/encoders/enctest/keyvals/device_country"

Response:
{"statusCode":"OK","statusString":"OK","values":{"dataframe":{"attributeNames":["Value","Type","Count","Sum"],"data":[["","S","21","0.0"],["703","S","1","0.0"],["a1","S","6","0.0"],["a2","S","488","12.0"],["af","S","2","0.0"], (…)
]}}}
```

## Encoder Modify Specific Key Values

This API function lets you modify specific key values in the Encoder. When invoking this REST endpoint, the server expects to receive the following columns in the [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) in the order shown below.

1. Key value to change count and/or sum
2. Type
3. New Count
4. New Sum

### URL

```
POST /symetry/rest/{cid}/encoders/{encodername}/keyvals/{key}/setValue [body=DataFrame]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/encoders/enctest/keyvals/device_country/setValues

Body:
{"attributeNames":["Value","Type","Count","Sum"],"data":[["ap","S","400","6"]]}

Response:
{"statusCode":"OK","statusString":"OK","values":{}}
```

## Encoder Create Specific Key Values

This API function lets you create new key values in the Encoder. When invoking this REST endpoint, the server expects to receive the following columns in the [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) in the order shown below.

1. Key to add a value. If the key does not exist, it will be created.
2. Key value to add count and sum.
3. Key type.
4. Count for the new key.
5. sum for the new key.

### URL

```
POST /symetry/rest/{cid}/encoders/{encodername}/createKeyValues [body=DataFrame]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/encoders/enctest/createKeyValues"

Body:
{"attributeNames":["Key","Value","Type","Count","Sum"],"data":[["device_country","ap","S","400","6"]]}

Response:
{"statusCode":"OK","statusString":"OK","values":{}}
```

## List Customer Encoders

This API function returns a list of encoder names that were created by a given user.

### URL

```
GET /symetry/rest/{cid}/encoders
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

### HTTP Response Entity

[StringList](appendix-a-json-data-structure-schema.md#stringlist)

### Sample Request/Response

Request:

```
GET url="http://charm:8080/symetry/rest/c1/encoders"

Response:
{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["enctest","smaato12"]}}}
```
