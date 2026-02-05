# Stream Data Source API

Data source described in the [Data Source API](data-source-api.md) section all are _finite_ type of data source int he sense that they all have a _finite_ number of rows. SymetryML also support _Stream Data Source_ that in theory can contain an infinite number of rows. Currently, both Kafka and NATS Stream Data Sources are supported.

## Kafka Streams

To use a Kafka stream data source, create a JSON data structure described in [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) that contains the fields in the table [below](stream-data-source-api.md#fields-required-to-create-a-stream-dsinfo-data-structure). Please note that for SymetryML to be able to read the data in your topic it needs to use both the `KafkaAvroDeserializer` as well as the Kafka Registry.

### Fields Required to Create a Stream DSInfo Data Structure

| Field    | Description                                                                                                                                                                                                                                     |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **type** | **Type of Data Source** - `kafka` for Kafka streams or `nats` for NATS streams                                                                                                                                                                 |
| **name** | Name of the data source.                                                                                                                                                                                                                        |
| **path** | path of the file / entity                                                                                                                                                                                                                       |
| **info** | **Hash Map** Containing Information needed to create a Stream. Please consult [Kafka Stream Additional Information](stream-data-source-api.md#kafka-stream-additional-information) or [NATS Stream Additional Information](#nats-stream-additional-information) for details |

## Kafka Stream Additional Information

| Key                                       | Required / Optional | Description                                                                                                                                                                                         |
| ----------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **bootstrap.servers**                     | Required            | Kafka Stream configuration parameter.                                                                                                                                                               |
| **schema.registry.url**                   | Required            | Kafka Stream configuration parameter.                                                                                                                                                               |
| **kafka.topic**                           | Required            | Kafka Stream configuration parameter.                                                                                                                                                               |
| **kafka.partitions**                      | Optional            | Kafka Stream configuration parameter. Default to all partitions. The list of partition to use must be defined as a list of comma separated integers. For instance `0,2,4,6,8` or `0,1,2,6,7,10,12`. |
| **kafka\_stream\_time\_between\_persist** | Optional            | Default to 300 seconds, that is 5 minutes. How long to wait between when the Symetry Project will persist its state. To disable this pass -1.                                                       |
| **enable.auto.commit**                    | Optional            | Kafka Stream configuration parameter. Default to true                                                                                                                                               |
| **auto.commit.interval.ms**               | Optional            | Kafka Stream configuration parameter. Default to 1000 ms.                                                                                                                                           |
| **auto.offset.reset**                     | Optional            | Kafka Stream configuration parameter. Default to _earliest_                                                                                                                                         |
| **Any other kafka parameter**             | Optional            | Any Kafka parameters can be used as well. One needs to prefix them with `sml.kafka.` e.g. `sml.kafka.client.dns.lookup` or `sml.kafka.fetch.min.bytes`                                              |

## NATS Streams

NATS is a lightweight, high-performance messaging system that provides both publish-subscribe and distributed queueing capabilities. SymetryML can connect to NATS servers to consume streaming data in real-time.

### NATS Stream Additional Information

To use a NATS stream data source, create a JSON data structure described in [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) with `type` set to `nats` and include the following fields in the `info` map:

| Key                            | Required / Optional | Description                                                                                                                                                                 |
| ------------------------------ | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **nats_hosts**                 | Required            | NATS server URL(s) to connect to. For example: `nats://localhost:4222`. Multiple hosts can be specified as a comma-separated list.                                         |
| **nats.subject**               | Required            | NATS subject to subscribe to. The subject determines which messages the stream will receive.                                                                                |
| **data.format**                | Required            | Format of the data in the NATS messages. Supported values: `json`, `csv`, or `protobuf`                                                                                     |
| **protobuf.schema**            | Required (protobuf) | Protobuf schema definition. Required only when `data.format` is set to `protobuf`.                                                                                          |
| **protobuf.msg.type.name**     | Required (protobuf) | Protobuf message type name to deserialize. Required only when `data.format` is set to `protobuf`.                                                                           |
| **nats_sec_user**              | Optional            | Username for NATS authentication. Use for user/password authentication.                                                                                                      |
| **nats_sec_passwd**            | Optional            | Password for NATS authentication. Use in combination with `nats_sec_user`.                                                                                                   |
| **nats_sec_token**             | Optional            | Token for NATS authentication. Use for token-based authentication.                                                                                                           |
| **nats_sec_nkey_seed**         | Optional            | NKey seed for NATS authentication. Use for NKey-based authentication.                                                                                                        |
| **nats_req_timeout**           | Optional            | Request timeout in seconds. Default is `30` seconds.                                                                                                                         |
| **nats_stream_max_memory**     | Optional            | Maximum memory (in bytes) for the NATS stream buffer. Default is `1073741824` (1 GB).                                                                                       |

### NATS Data Formats

NATS streams support three data formats:

1. **JSON** - Messages are expected to be in JSON format with attribute names matching the project schema
2. **CSV** - Messages are expected to be comma-separated values (or using custom delimiters defined with additional CSV options)
3. **Protobuf** - Messages are serialized using Protocol Buffers. Requires both `protobuf.schema` and `protobuf.msg.type.name` to be specified

### Example NATS DSInfo (JSON Format)

```json
{
  "type": "nats",
  "name": "my-nats-stream",
  "info": {
    "nats_hosts": "nats://localhost:4222",
    "nats.subject": "sensor.data",
    "data.format": "json"
  }
}
```

### Example NATS DSInfo (Protobuf Format)

```json
{
  "type": "nats",
  "name": "my-protobuf-stream",
  "info": {
    "nats_hosts": "nats://nats-server:4222",
    "nats.subject": "telemetry.metrics",
    "data.format": "protobuf",
    "protobuf.schema": "syntax = \"proto3\"; message Metrics { double temperature = 1; double pressure = 2; }",
    "protobuf.msg.type.name": "Metrics"
  }
}
```

## Stream Data Source Encryption

Same as one need to encrypt normal data source, stream data source information needs to be encrypted. Please consult the [Data Source Encryption](data-source-api.md#data-source-encryption) for details.

## Stream Data Source Create

This API function creates a new stream data source and attach it to a SymetryML project - the owner project. Once created the new stream data source will continuously pull data from Kafka and then push the new data tuple into SymetryML Project in a streaming fashion.

### URL

```
POST /{cid}/projects/{pid}/streams/create [Body=DSInfo (encrypted)]
```

| Parameter         | Required / Optional | Description                                                     |
| ----------------- | ------------------- | --------------------------------------------------------------- |
| **fromBeginning** | Optional            | if true then start streaming data from beginning of the stream. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                  |
| ---------------- | ------------------- | ------------------------------------------------------------ |
| **202**          | CREATED             | Success.                                                     |
| **409**          | CONFLICT            | A stream data source with the specified name already exists. |

## Stream Data Source Browse

This methods allows you to browse available stream on your stream server. For Kafka this means listing topic that are available.

### URL

```
POST /{cid}/streams/browse [Body=DSInfo (encrypted)]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                                     | Description                                             |
| ---------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| [**DSListingResponse**](appendix-a-json-data-structure-schema.md#dslistingresponse-json) | Contains listing information from the streaming server. |

## Stream Data Source Preview

This methods allows you to preview a sample of the data available on a given stream.

### URL

```
POST /{cid}/streams/preview [Body=DSInfo (encrypted)]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                     | Description                                |
| ------------------------------------------------------------------------ | ------------------------------------------ |
| [**DataFrame**](appendix-a-json-data-structure-schema.md#dataframe-json) | Dataframe containing a preview of the data |

## Stream Data Source Metrics

This rest endpoint return information about a stream. Number of rows processed, tuples / secs processed, etc… Information varies with the type of the stream.

### URL

```
GET /{cid}/projects/{pid}/streams/{sid}/metrics
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                            | Description                          |
| --------------------------------------------------------------- | ------------------------------------ |
| [**KSVSMap**](appendix-a-json-data-structure-schema.md#ksvsmap) | A map with key, value as string pair |

## Stream Data Source Start

This rest endpoint start / resume a stream data source. That is start pulling data and push it into the owner SymetryML project.

### URL

```
GET /{cid}/projects/{pid}/streams/{sid}/start
```

| Parameter         | Required/Optional? | Description                                                     |
| ----------------- | ------------------ | --------------------------------------------------------------- |
| **fromBeginning** | Optional           | if true then start streaming data from beginning of the stream. |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Stream Data Source Stop

This rest endpoint stop a stream data source. Data will not be pushed anymore to the owner SymetryML project.

### URL

```
GET /{cid}/projects/{pid}/streams/{sid}/stop
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

## Delete a Stream

Delete a stream from a project. If the stream is running it will first be stopped.

### URL

```
DELETE /symetry/rest/{cid}/projects/{pid}/streams/{sid}
```

### Canonical URL Parameters

| Parameter | Description |
| --------- | ----------- |
| **cid**   | Customer ID |
| **pid**   | Project ID  |
| **sid**   | Stream ID   |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success     |

## Stream Data Source Error Log

This rest endpoint return a list of error for a given stream. Since stream happen asynchronously in the background, it allows to check for any problems with a given stream

### URL

```
GET /{cid}/projects/{pid}/streams/{sid}/errorLog
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Description                     |
| --------------------------------------------------------------------- | ------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | A list of error for that stream |

## Stream Data Source List

This rest endpoint return a list of streams name that belong to a given project for a given user.

### URL

```
GET /{cid}/projects/{pid}/streams/list
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Description                       |
| --------------------------------------------------------------------- | --------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | A list of stream data source name |
