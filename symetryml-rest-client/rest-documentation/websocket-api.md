# WebSocket API

## WebSocket Overview

SymetryML supports the use of WebSocket technology on a subset of its API. This API can be used when lower latency is needed. Each WebSocket call contains a set of 3 pieces of information:

* The header length
* The headers, which are used to authenticate the request.
* The message payload.

The message are string formatted using the following protocol

* `<Header-length>,<header><payload>` ex:
* `65,{headers=['some date','some md5','some authorization','some customer id'],extraKeys={}}{PAYLOAD}`

The following table describes this information.

| **Header Length**     | Length of the header section.                                                                                                                                                                                                                                                                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Header**            | <p>A JSON string containing 2 pieces of information  - header[4] = an array of 4 string used for authorization.  </p><p>- date  </p><p>- md5  </p><p>- authorization hash code.  </p><p>- customer id  </p><p>- extraKeys = contains extra information that depends on the WebSocket endpoint invoked.  This Header field is mandatory on the first WebSocket message only.</p> |
| **WebSocket Payload** | A JSON string representing a dataframe. See the [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) for more details                                                                                                                                                                                                                                           |

### About WebSocket Authorization

With WebSocket, the following string must be used to create the Authorization hash code.

| Item                | Description                                                                                                                                           |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Authorization**   | Signature                                                                                                                                             |
| **Signature**       | `Base64( HMAC-SHA2(UTF-8-Encoding-Of( StringToSign ) ) );`                                                                                            |
| **StringToSign**    | `Content-MD5 + "\n"  SymetryML-Secret-Key + "\n"  Sym-Date Header + "\n"  SymetryML-key-Id + "\n"  RequestBody + "\n"  ServicePath + "\n"  extraKeys` |
| **Sym-Date format** | See the section [Symetry Date Header](symetryml-rest-api-security.md#symetryml-date-header)                                                           |
| **ServicePath**     | Path of the URL to be invoked (for instance /symetry/ws/predict).                                                                                     |
| **extraKeys**       | Each extra key should be included individually on a separate new line. See example below for details.                                                 |

#### Example of WebSocket String to Sign

```
H47KwjgTU5G7v8BJS+ttgg== + '\n'
<SECRETKEY> + '\n'
2014-10-24 17:24:04 + '\n'
c1 + '\n'
{"attributeNames":["sepal_length","sepal_width","petal_length"],"data":[["4.3","3","1.1"]],"attributeTypes":["C","C","C"]} + '\n'
/symetry/ws/predict + '\n'
irisTest + '\n'
testLDA
```

## Learning Using WebSocket

This API function call allows users to learn a dataframe using WebSocket services.

### URL

```
ws://SERVER_HOST/symetry/ws/learn
```

### Extra Parameter

| Index | Short Description    | Description                                |
| ----- | -------------------- | ------------------------------------------ |
| **0** | SymetryML Project ID | The name of the project to learn the data. |

### WS Response

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### Sample WS Request

```
Request URL:
ws://charm:8080/symetry/ws/learn
Request Message:
{"headers":["2014-10-24 14:28:03","PuAtwbvTrwlX8JQHWvtv7Q\u003d\u003d","Rz/EO7GXX/KE2Jj52+kePmIrudA\u003d","c1"],"extraKeys":["irisTest"]}-289907289{"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"data":[["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"],["4.9","3.
(...)
5","0","1","0","1","0","1","0","1","0","0","1"],["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]}
```

## Forgetting Using WebSocket

This API function call allows users to forget a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) of data using WebSocket services.

### URL

```
ws://SERVER_HOST/symetry/ws/forget
```

### Extra Parameter

| Index | Short Description    | Description                                |
| ----- | -------------------- | ------------------------------------------ |
| **0** | SymetryML Project ID | The name of the project to learn the data. |

### WS Response

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Predicting Using WebSocket

This API function call allows users to predict using WebSocket services.

### URL

```
ws://SERVER_HOST/symetry/ws/predict
```

### Extra Parameter

| Index | Short Description    | Description                                |
| ----- | -------------------- | ------------------------------------------ |
| **0** | SymetryML Project ID | The name of the project to learn the data. |
| **1** | Model Id             | The model name to use to make predictions. |

### WS Response

On success this WebSocket call will return a JSONResponseClient containing a [KSVSMap](appendix-a-json-data-structure-schema.md#ksvsmap) entity. The format of this response is the same as with the SymetryML REST prediction API described in [Prediction API](prediction-api.md).

### Sample WS Request

```
Request URL
ws://charm:8080/symetry/ws/predict

Request Message:
{"headers":["2014-10-24 17:17:58","H47KwjgTU5G7v8BJS+ttgg\u003d\u003d","/Xd/JypB3rDQ+1BVaZRm6n35kt0\u003d","c1"],"extraKeys":["irisTest","testLDA"]}-289907289{"attributeNames":["sepal_length","sepal_width","petal_length"],"data":[["4.3","3","1.1"]],"attributeTypes":["C","C","C"]}

Response:
{"statusCode":"OK","statusString":"OK","values":{"KSVSMap":{"values":[{"res":"1","normZ":"0.2929126883636288","resZ":"-0.4559922893809558","z0":"-3.840891864408894","z1LTz0":"false"}]}}}
```
