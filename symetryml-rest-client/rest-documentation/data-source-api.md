# Data Source API

## Using a Data Source

In SymetryML, a data source is an abstraction of a CSV file that resides somewhere and that can be used by:

* SymetryML projects to learn new data
* Models to make predictions and assessments
* Encoder to update their internal encoding table

SymetryML supports various types of data sources:

* Secure File Transfer Protocol (SFTP)
* HTTP/HTTPS URL
* Amazon Simple Storage Service (S3)
* Microsoft Azure Blob Storage
* Google Cloud Storage
* Oracle OCI Object Storage
* Amazon RedShift
* Spark Processing: Amazon S3, Google cloud storage, Oracle OCI object storage and Microsoft Azure blob storage data sources can be processed in parallel leveraging a Spark Cluster.
* SymetryML data source plugins
* JDBC
* Local Data Source, that is allows to browse the local file system of the jetty web server with same privileges as the user running the Jetty web server.

To use a data source, create a JSON data structure described in [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) that contains the fields in table [below](data-source-api.md#required-dsinfo-fields).

### Required DSInfo Fields

| Field    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **type** | <p><strong>Type of Data Source</strong></p><p>- Secure FTP (SFTP) data source = <strong>sftp</strong></p><p>- HTTP/HTTPS data source = <strong>http</strong></p><p>- Amazon S3 = <strong>s3</strong></p><p>- Oracle OCI Object Storage with S3 Compatibility = <strong>s3oci</strong></p><p>- Google Clound Storage = <strong>gcs</strong></p><p>- Amazon Redshift = <strong>redshift</strong></p><p>- Spark Data Source = please see the <a href="data-source-api.md#spark-map-reduce">Spark Data Source type</a> section for the matrix of all possible data source names involving Spark Processing.</p><p>- Data Source Plug ins. - <strong>jdbc</strong></p><p>- Local file = <strong>localfile</strong></p><p>- Amazon Elastic Map Reduce <strong>= emr</strong></p><p>- Microsoft Azure Blob Storage = <strong>abs</strong></p> |
| **name** | Name of the data source.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **info** | Hash Map Containing Additional Information Based on Data Source. Please consult the [next section](data-source-api.md#additional-information-stored-in-data-source) for details about this field.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

### Additional Information Stored in Data Source

The `info` field of a data source contains specific information based on the type of data source. The following tables describes what they are for each different type of data source

#### HTTP/HTTPS Data Source

| field  | Description              |
| ------ | ------------------------ |
| `path` | http:// or https:// URL. |

#### Secure FTP (SFTP)

| field        | Description                                       |
| ------------ | ------------------------------------------------- |
| `path`       | path to the file on the server.                   |
| `sftpuser`   | user name used to connect to the SFTP server.     |
| `sftppasswd` | user password used to connect to the SFTP server. |
| `sftphost`   | host to which you want to connect.                |

#### Amazon S3

| field         | Description                                                |
| ------------- | ---------------------------------------------------------- |
| `path`        | path to file on the server, excluding the Amazon S3 bucket |
| `s3accessKey` | Amazon S3 access key to use to connect to S3.              |
| `s3secretKey` | Amazon S3 secret key to use to connect to S3.              |
| `s3bucket`    | Amazon S3 bucket to use.                                   |

#### Oracle OCI Object Storage with S3 Compatibility

Please consult [https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm) for details on how to configure your Oracle OCI account so that the Amazon S3 Compatibility API can be used.

| field          | Description                                                                                 |
| -------------- | ------------------------------------------------------------------------------------------- |
| `path`         | path to file on the server, excluding the Oracle OCI Object Storage bucket                  |
| `s3accessKey`  | Oracle Oracle OCI Object Storage access key to use to connect to Oracle OCI Object Storage. |
| `s3secretKey`  | Oracle Oracle OCI Object Storage secret key to use to connect to Oracle OCI Object Storage. |
| `s3bucket`     | Oracle Oracle OCI Object Storage bucket to use.                                             |
| `ocinamespace` | Oracle Oracle OCI Object Storage namespace                                                  |
| `ociregion`    | Oracle Oracle OCI Object Storage region                                                     |

#### Google Cloud Storage

| field          | Description                                                                                                                                                   |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path`         | Path to file                                                                                                                                                  |
| `gcsaccessKey` | GCS [HMAC](https://cloud.google.com/storage/docs/authentication/hmackeys) Access Key                                                                          |
| `gcssecretKey` | GCS [HMAC](https://cloud.google.com/storage/docs/authentication/hmackeys) Secret Key                                                                          |
| `gcsbucket`    | GCS Bucket                                                                                                                                                    |
| `gcsproject`   | GCS Project                                                                                                                                                   |
| `gcsmarker`    | Optional marker parameter indicating where in the GCS bucket to begin listing. The list will only include keys that occur lexicographically after the marker. |
| `gcsdelimiter` | GCS File/Folder delimiter. _/_ is used by default                                                                                                             |

#### Microsoft Azure Blob Storage

| field                                         | Description                                                                                                                                                 |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path`                                        | Path to file                                                                                                                                                |
| `azure.credentials.connection.string`         | Connection string that specifies credentials to authorize access to Azure Blob Storage. Use one of this, account key or SAS token.                          |
| `azure.account.name`                          | Name of the Azure account to use                                                                                                                            |
| `azure.credentials.sharedkey.account.key`     | Account key that specifies credentials to authorize access to Azure Blob Storage. Use one of this, connection string or SAS token.                          |
| `azure.credentials.sharedkey.sas.token`       | SAS token (account or service) that specifies credentials to authorize access to Azure Blob Storage. Use one of this, connection string or account key.     |
| `azure.blob.container.name`                   | Name of the Azure Blob Storage container that contains the blob                                                                                             |
| `azure.blob.inputstream.chunk.size.max.bytes` | Maximum size in bytes of each chunk of data when reading the blob contents chunk by chunk. Default: 4194304                                                 |
| `azure.blob.path.delimiter`                   | String that separates elements of the path to the blob file. Default: /                                                                                     |
| `azure.blob.list.marker`                      | <p>Marker that specifies the beginning of the next page of a list of<br>Azure Blob Storage items to fetch from Azure. This property is used internally.</p> |

#### Amazon Redshift

| field      | Description               |
| ---------- | ------------------------- |
| `path`     | name of the table to use. |
| `rsuser`   | Redshift database user    |
| `rspasswd` | Redshift user password    |
| `rsurl`    | Redshift connection url   |

#### Spark Map Reduce

| field                                                                                     | Description                                                                                                                                                                   |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sparkmaster`                                                                             | address of the spark’s cluster master                                                                                                                                         |
| `spark.job.process.jvm.heap.size.min`                                                     | Mininum JVM size used for the spark Driver process launched by the Jetty Rest Server. Default : 1024m                                                                         |
| `spark.job.process.jvm.heap.size.max`                                                     | Maximum JVM size used for the spark Driver process launched by the Jetty Rest Server. Default: 2048m                                                                          |
| Any Spark parameters be used also like: `spark.executor.memory` or `spark.executor.cores` | To pass such parameters, prefix them with ‘sml.sparkenv.’ as in the following examples: - sml.sparkenv.spark.executor.cores - sml.sparkenv.spark.cores.max                    |
| `spark.automl.sample.random.seed`                                                         | If AutoML is used, one can set the randomizer seed that will be used to select a random sample of tuple from the data source to be used to bootstrap the AutoML environement. |

**Spark Map Reduce Data Source Type**

Please note that the following matrix of supported version of Spark versus data source as well as how to name the data source for a given combination:

| Data Source              | Spark 2.4.5 hadoop 2.7 | Spark 2.4.6 hadoop 2.7 | Spark 3.0.1 hadoop 2.7 | Spark 3.0.2 hadoop 3.2  |
| ------------------------ | ---------------------- | ---------------------- | ---------------------- | ----------------------- |
| **Oracle OCIs3**         | N                      | N                      | N                      | sparkocis3\_mr\_3\_0\_2 |
| **Amazon S3**            | sparks3\_mr\_2\_4\_5   | sparks3\_mr\_2\_4\_6   | sparks3\_mr\_3\_0\_1   | sparks3\_mr\_3\_0\_2    |
| **Google Cloud Storage** | sparkgcs\_mr\_2\_4\_5  | sparkgcs\_mr\_2\_4\_6  | sparkgcs\_mr\_3\_0\_1  | sparkgcs\_mr\_3\_0\_2   |
| **Microsoft Azure Blob** | sparkabs\_mr\_2\_4\_5  | sparkabs\_mr\_2\_4\_6  | sparkabs\_mr\_3\_0\_1  | sparkabs\_mr\_3\_0\_2   |

#### JDBC

| field    | Description |
| -------- | ----------- |
| driver   |             |
| host     |             |
| port     |             |
| database |             |
| user     |             |
| password |             |

#### Amazon EMR

<table><thead><tr><th>field</th><th width="104.33333333333331">Required</th><th>Description</th></tr></thead><tbody><tr><td><code>chunksize</code></td><td>Optional</td><td>SymetryML process the data <em>chunk by chunk</em>. This parameters specifies the <em>chunk</em> size. <strong>Default: 5000</strong></td></tr><tr><td><code>emr.client.aws.region</code></td><td>Optional</td><td>AWS region of the EMR cluster. <strong>Default: us-east-1</strong></td></tr><tr><td><code>emr.cluster.ec2.key.name</code></td><td><strong>Required</strong></td><td>EC2 key pair name for the cluster.</td></tr><tr><td><code>emr.cluster.ec2.subnet.id</code></td><td>Optional</td><td>EC2 subnet id for the cluster. <strong>Default: null</strong></td></tr><tr><td><code>emr.cluster.instance.count</code></td><td><strong>Required</strong></td><td># of EC2 instances in the EMR cluster.</td></tr><tr><td><code>emr.cluster.instance.master.type</code></td><td><strong>Required</strong></td><td>Instance type of the master EC2 instance.</td></tr><tr><td><code>emr.cluster.instance.slave.type</code></td><td><strong>Required</strong></td><td>Instance type of the slave EC2 instances.</td></tr><tr><td><code>emr.cluster.log.storage.enable</code></td><td>Optional</td><td>Boolean enabling for storing the EMR logs. <strong>Default: false</strong></td></tr><tr><td><code>emr.cluster.log.storage.uri</code></td><td>Optional</td><td>URI of the EMR logs. <strong>Default: null</strong></td></tr><tr><td><code>emr.job.flow.role</code></td><td>Optional</td><td>EMR role for EC2 that is used by EC2 instances within the cluster. <strong>Default: AWS EMR_EC2_DefaultRole</strong></td></tr><tr><td><code>emr.s3.job.bucket.name</code></td><td><strong>Required</strong></td><td>S3 bucket that stores the files needed for the Spark cluster job; it can include the directory that stores the EMR logs.</td></tr><tr><td><code>emr.service.role</code></td><td>Optional</td><td>Amazon EMR role, which defines the allowable actions for Amazon EMR. <strong>Default: AWS EMR_DefaultRole</strong></td></tr><tr><td><code>path</code></td><td><strong>Required</strong></td><td>Path of data source to process, can be a folder. This is the 'data path' without the 'bucket part'</td></tr><tr><td><code>s3accessKey</code></td><td><strong>Required</strong></td><td>AWS access key</td></tr><tr><td><code>s3bucket</code></td><td><strong>Required</strong></td><td>AWS S3 bucket where data resides</td></tr><tr><td><code>s3marker</code></td><td><strong>Required</strong></td><td>Optional marker parameter indicating where in the S3 bucket to begin listing. The list will only include keys that occur lexicographically after the marker.</td></tr><tr><td><code>s3secretKey</code></td><td><strong>Required</strong></td><td>AWS secret key</td></tr><tr><td><code>sml.sparkenv.*</code></td><td><strong>Required</strong></td><td>Allows to specify any Apache Spark environment configuration like: <code>spark.cores.max</code> e.g. use:<strong><code>sml.sparkenv.spark.cores.max</code></strong> or <code>spark.executor.memory</code> e.g. use:<strong><code>sml.sparkenv.spark.executor.memory</code></strong></td></tr><tr><td><code>sparksymproject</code></td><td><strong>Required</strong></td><td>Name of the project.</td></tr></tbody></table>

### Additional CSV Options

You can specify additional parameters that describe the ‘type of csv files’. You can also add the following parameters to a data source to change how SymetryML parses your data:

| Key                       | Description                                                                                                                                  |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **csv\_entry\_separator** | Specifies which character to use as the delimiter for each record for a given tuple.                                                         |
| **csv\_quote\_character** | Specifies the quote character.                                                                                                               |
| **csv\_strict\_quotes**   | Setting this option to true discards characters outside the quotes. If there are no quotes between delimiters, an empty string is generated. |
| **csv\_header\_missing**  | Specifies that this data source does not have any header. SymetryML can then generate a header automatically.                                |

### Additional Information on Spark S3 Data Source

SymetryML can leverage a spark cluster to speed up processing of large amounts of data significantly. Currently, your data must reside on Amazon S3. Depending on the size of your data, it may take more or less time for the job to start, as the Spark Cluster must compute the partitions of your data before starting the job. Consequently, if your data is very large, this may take a few minutes.

Please be sure to consult the [Spark Installation Guide](../../guides/installation-guide/spark-installation-guide.md) for additional Spark information - and more particularly the [Additional SymetryML Configuration for Spark Support](../../guides/installation-guide/spark-installation-guide.md#additional-symetryml-configuration-for-spark-support) section.

**Best practices for Spark S3 Data Source:**

* Performance may vary depending on Amazon resource utilization when you run your job.
* Be sure all executor nodes in your cluster reside in the same Amazon EC2 placement group.

### About Data Source Plugins (DSPlugins)

The SymetryML data source API allows you to create a new data source by the form of Java library (jar) that can be added the server. Instead of transforming data into CSV files, for example, you can write a DS plugin that reads the data natively.

## Data Source Encryption

Data sources might contain sensitive information that should never be passed in the clear. To avoid having to use HTTPS for these services, the SymetryML REST API forces you to pass such information in encrypted form. This can be done easily, as each SymetryML secret key is also a 128-bit Advanced Encryption Standard (AES) secret key.

1. Create your [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) data structure and enter the appropriate information so that the SymetryML server can access it.
2. Extract the JSON string from that data structure.
3. Encrypt the JSON string representation using your SymetryML secret key:
   * Initialization vector in Base 64: **LzM5QUtXZXWHm7HJ4wAePg==**
   * Block cipher algorithm: **AES/CBC/PKCS5Padding**
4. Send the encrypted string as part of the body to any REST service that requests a [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) as the body.
5. The server decrypts the string using the client secret key and reconstructs the [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json).

See [Appendix B](https://github.com/symetryml/smldocgb/blob/master/symetry-rest/rest-documentation/appendix-b-sample-code.md) - for code example on how to perform this encryption in Java, JavaScript, or Python.

## Data Source Create

This API function creates a new data source.

### URL

```
POST /symetry/rest/{cid}/dss/create [Body=DSInfo (encrypted)]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                           |
| ---------------- | ------------------- | ----------------------------------------------------- |
| **202**          | CREATED             | Success.                                              |
| **409**          | CONFLICT            | A data source with the specified name already exists. |

### HTTP Response Entity

None.

### Sample Request/Response

Please note that in the following example the BODY is encrypted. Refer to section [Data Source Encryption](data-source-api.md#data-source-encryption) for encryption details.

```
Request:
POST url="http://charm:8080/symetry/rest/c1/dss/create"

Body:
emUmJ7LXKaICPww/dKRoMR/Go6+B3ATTn+GwgE1/vcK9pN/mLoqGyKiMtGiTYMct4Gth1ElniKLXtmXfHfs7Rfn+QhJHZ+s00w2PBdbvYZIoF3My04H5XCboY21Fh4SkBhsxo+DhYuardN7R+uGphN/DSbiHRLIXe51HijSpfuq0fJuQYW0ccN4FM/B74LJccuDnbb+IouS9u/9rybKu/wsVbDVRTK/zZpWXyn4qtds=

Response:
{"statusCode":"CREATED","statusString":"DS created with name [ds1] for customer [c1]","values":{}}
```

## Data Source Update

This API function update an existing data source.

### URL

```
PUT /symetry/rest/{cid}/dss/{dsname} [Body=DSInfo (encrypted)]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                            |
| ---------------- | ------------------- | ------------------------------------------------------ |
| **200**          | OK                  | Success.                                               |
| **404**          | NOT FOUND           | If data source with the specified name does not exist. |

### HTTP Response Entity

None.

### Sample Request/Response

Please note that in the following example the BODY is encrypted. Refer to section [Data Source Encryption](data-source-api.md#data-source-encryption) for encryption details.

```
Request:
PUT url="http://charm:8080/symetry/rest/c1/dss/aDataSourceName"

Body:
emUmJ7LXKaICPww/dKRoMR/Go6+B3ATTn+GwgE1/vcK9pN/mLoqGyKiMtGiTYMct4Gth1ElniKLXtmXfHfs7Rfn+QhJHZ+s00w2PBdbvYZIoF3My04H5XCboY21Fh4SkBhsxo+DhYuardN7R+uGphN/DSbiHRLIXe51HijSpfuq0fJuQYW0ccN4FM/B74LJccuDnbb+IouS9u/9rybKu/wsVbDVRTK/zZpWXyn4qtds=

Response:
{"statusCode":"OK","statusString":"DS updated with name [ds1] for customer [c1]","values":{}}
```

## List Customer Data Sources

This API function returns all the data sources that belong to a user.

### URL

```
GET /symetry/rest/{cid}/dss/
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                  | Example                                                                                                                                                                                                           |
| --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**StringList**](appendix-a-json-data-structure-schema.md#stringlist) | `{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["Iris_SymetryML.csv-predict.csv:s3","h1:http","BigData11g_Test.csv:http","Iris_SymetryML.csv:s3","Smaato_Bids_20130812_CTR.csv:s3"]}}}` |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/dss"

Response:=
{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["Iris_rtlm-out-neil.csv:s3","Iris_rtlm.csv:s3","BigData11g_num.csv:s3","Smaato_Bids_20130812_CTR.csv:s3"]}}}
```

## Delete Data Source

This API function deletes a data source.

### URL

```
DELETE /symetry/rest/{cid}/dss/{dsname}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                    |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success.                                                                                                                                       |
| **409**          | CONFLICT            | Data source cannot be deleted. A SymetryML project might be using the data source. The response contains an error string with further details. |

### HTTP Response Entity

None

## Data Source Information

This API function returns information about a data source. Because this information can be sensitive, the server encrypts it to ensure that the information will be returned safely, even when using HTTP. Use your SymetryML secret key to decrypt the information. For information about how to decrypt the response, see the section [Data Source Encryption](data-source-api.md#data-source-encryption).

### URL

```
GET /symetry/rest/{cid}/dss/{dsname}
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity   | Example                                                                                                 |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| **DSInfo (encrypted)** | [DSInfo](appendix-a-json-data-structure-schema.md#dsinfo-json) encrypted using the customer secret key. |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/dss/BigData11g_num.csv"

Response:
{"statusCode":"OK","statusString":"OK","values":{"dsinfo":"emUmJ7LXKaICPww/dKRoMRNJEMepL37Eq9CgZfhZPWj93mo3A+C8ucfIOGaPwwn2dip/JEuLFjUT/fjHjy18XKFnzFz5Ujp0WmS0uA4ssvAJwNPL6BvnsY6+a/lKa+c/q9/5tz5lr13N13I7OGAhuYhXYV+xb8oFZqsn+bH5spBXRb5u+oyEMXNKLCaNt3pzc/xCyW47KCwIi9V5iSA+fcJAWfetm9ZsIHNbI6utkxKrqrU5OfLmgriGAP++yQtutlGR7r/bKV1bRc8UDsgsXQg1HgoxHcKXCgsAFDFzqsJmZ/5/uQDc0ytc5Fk85GUx"}}
```

## Data Source Browsing

This API function lists content about a remote data source directory.

### URL

```
POST /symetry/rest/{cid}/projects/dsbrowse/ [Body=DSListingRequest (encrypted)]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                                     | Description                                                           |
| ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| [**DSListingResponse**](appendix-a-json-data-structure-schema.md#dslistingresponse-json) | Contains listing information about the requested directory or folder. |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/dsbrowse"

Body:
drhSjndw6G15pgevCsDqaSfjX9x3hMo+dNqd/MV943Dsd2rl2guhvq2qUhjEORcfKAEjHaoRZMKmSbQB6bcca2YT6HmUyRxuOG0wiKgGy0MOEq7+iIncbX4orpGr4rhro1Frw909Uy8qcWskaInQHJT4EGRPcwxvwInFlea39hsMkycFK4pKlTpanOUYgcv7

Response:
{"statusCode":"OK","statusString":"OK","values":{"dsdirectoryListing":{"ok":true,"dirs":["data2/","folder-a/","folder-datasets/","folder-demo/","folder-dev/","folder-dev-pub-http/","folder-dev-pub-https/","folder-docs/","folder-perf-all-reports/","folder-source/"]}}}
```

## Fetching Sample Data 1

This API function fetches a data source sample by specifying the data source information as part of the request body. The response returns up to 128 lines. This REST call needs a request body that contains an encrypted DSInfo data structure (see the section [Data Source Encryption](data-source-api.md#data-source-encryption)).

### URL

```
POST /symetry/rest/{cid}/dss/sample/preview [body=DSInfo encrypted]
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity                                                     | Description                                           |
| ------------------------------------------------------------------------ | ----------------------------------------------------- |
| [**DataFrame**](appendix-a-json-data-structure-schema.md#dataframe-json) | Contains a sample of the data source up to 128 lines. |

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/dss/sample/preview

Body:
WTqUHBoXHbl+cMMjdc0zgjBP8e44G1os15V+I4GZgDOr1dX9uOfvY5uK9ZgC9yral9XC1ohD1W+UvkPlKR4dQT00EgCdS2UPgZz2NwwooHOM+KY1Ysf5qZlkFKiOkxwoWH/mr3mvvgdTUpZS8zrDJk3gwsavFT5fe0J2lTR33F1OH7FwxP4qs5nzRbVz546l

Response:
{"statusCode":"OK","statusString":"OK","values":{"dataframe":{"attributeNames":["adexchange","imp_width","imp_height","imp_btype","preference","pub","domain","site_base_url","category","device_ip","device_country","device_dma","device_state","device_city","zip","device_carrier","language","device_os","device_make","device_model","device_osv","lat","lon","restriction_bcat","restriction_badv","position","gender","user_keyword","user_yob","user_age","view_count","campaign","creative","creative_type","winner","bid_price","win_price","clicked","rtlm_ctr_score","rtdm","req_hour","req_day","category_count","bcat_count","badv_count"],"data":[["Smaato","320","50","","APP","New IT Solutions Ltd","","","Technology \u0026 Computing","157.55.32.83","US","","WASHINGTON","REDMOND","98052","","","Unknown","","","","47.67398834","-122.1215134","IAB7-28|IAB19-30|IAB22-1|IAB19-3|IAB17-18|IAB26|IAB25|IAB24|IAB9-9|IAB7-

(...)

,"attributeTypes":["S","C","C","L","S","L","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","L","S","S","S","S","S","S","B","S","S","S","B","C","C","B","S","B","C","C","B","C","B"]}}}
```

## Fetching Sample Data 2

### URL

```
GET /symetry/rest/{cid}/dss/{dsname}/sample
```

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

### HTTP Response Entity

| HTTP Response Entity | Description                                                               |
| -------------------- | ------------------------------------------------------------------------- |
| **DataFrame**        | See [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json). |

### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/dss/Smaato_Bids_20130812_CTR.csv/sample"

Response:
{"statusCode":"OK","statusString":"OK","values":{"dataframe":{"attributeNames":["adexchange","imp_width","imp_height","imp_btype","preference","pub","domain","site_base_url","category","device_ip","device_country","device_dma","device_state","device_city","zip","device_carrier","language","device_os","device_make","device_model","device_osv","lat","lon","restriction_bcat","restriction_badv","position","gender","user_keyword","user_yob","user_age","view_count","campaign","creative","creative_type","winner","bid_price","win_price","clicked","rtlm_ctr_score","rtdm","req_hour","req_day","category_count","bcat_count","badv_count"],"data":[["Smaato","320","50","","APP","New IT Solutions Ltd","","","Technology \u0026 Computing","157.55.32.83","US","","WASHINGTON","REDMOND","98052","","","Unknown","","","","47.67398834","-122.1215134","IAB7-28|IAB19-30|IAB22-1|IAB19-3|IAB17-18|IAB26|IAB25|IAB24|IAB9-9|IAB7-

(...)

,"attributeTypes":["S","C","C","L","S","L","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","S","L","S","S","S","S","S","S","B","S","S","S","B","C","C","B","S","B","C","C","B","C","B"]}}}
```

## SymetryML Project Data Source API

### Add Data Source to a SymetryML project

This API function lets you add a data source to a project.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/addds/{dsname}
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

None

### Remove Data Source from a SymetryML project

This API function lets you remove a data source from a project.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/detachds/{dsname}
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description |
| ---------------- | ------------------- | ----------- |
| **200**          | OK                  | Success.    |

#### HTTP Response Entity

None

### Learning Data from a Data Source

This API function lets you learn from a previously created data source.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/dss/{dsname}/learn
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                           |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. Example: `{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}` |

#### HTTP Response Entity

None

#### Sample Request/Response

```
Request 1 create ds:
POST url=http://charm:8080/symetry/rest/c1/dss/create

Body 1:
{ENCRYPTED}

Request 2 learn ds:
POST url=http://charm:8080/symetry/rest/c1/projects/r1/dss/irisds/learn

BODY 2
{"errorHandling":1,"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"data":[],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]}

Response 2:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Response 2 Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Job 3 Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job 3 Response:
{"statusCode":"OK","statusString":"Job is finished","values":{"smlInfo":{"pid":"r1","isDirty":true,"modelsList":[],"modelTypeList":[],"attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"attributeIndexes":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],"attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"],"modelAssessments":{},"modelPredictions":{},"hash":-1,"categorySeparator":"$","type":"cpu","creationDate":1488220762857,"lastModificationDate":1488226030143,"loaded":true,"persisted":true}}}
```

### Forgetting Data from a Data Source

This API function lets you forget data from a previously created data source.

#### URL

```
GET /symetry/rest/{cid}/projects/{pid}/dss/{dsname}/forget
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                               |
| ---------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. For example: `{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}` |

#### HTTP Response Entity

None

#### Sample Request/Response

See the section previous [Learn](data-source-api.md#learning-data-from-a-data-source) endpoint and replace `learn` with `forget` in the url.

### Prediction Based on a Data Source

After a model is built, you can use this API function to make predictions using a data source. This action can be performed on very large files if they reside on Amazon S3. A prediction file is created that contains the original rows, along with additional prediction information based on the type of model used.

#### URL

```
Request:
POST /symetry/rest/{cid}/projects/{pid}/dss/predict/{modelid}?indsname={indsname}&outdsname={outdsname}&impute=false
Request Body:
{
  "attributeNames":[{input attributes names}],
  "attributeTypes":[{input attribute types}]
}
```

#### Query Parameters

| Parameter     | Required / Optional | Description                                                    |
| ------------- | ------------------- | -------------------------------------------------------------- |
| **indsname**  | Required            | Data source to use as input file for prediction.               |
| **outdsname** | Required            | Data source to use as output file for prediction.              |
| **impute**    | Optional            | Boolean parameter specifying whether to impute missing values. |

#### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                               |
| ---------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED              | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request.   |
| **500**          | INTERNAL SERVER ERROR | If the server refuses to accept the new job, it notifies the client with the error "Job execution was refused by server." |

#### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/projects/irisTest/dss/predict/testLDA?indsname=dsin&outdsname=dsout&impute=false
Request Body:
{
  "attributeNames":["sepal_length","sepal_width","petal_length","petal_width"],
  "attributeTypes":["C","C","C","C"]
}

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/2

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/2

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{"dataframe":{"attributeNames":["resZ_Result","res_Result","normZ_Result","sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],"data":[["-0.4559922893809558","1","0.2929126883636288","4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],["-0.5225734165408404","1","0.2965158290613048","

(…)

["-2.6526076600477295","1","0.4232667751179995","6.9","3.1","5.4","2.1","0","1","0","1","0","1","0","1","0","0","1"]]}}}
```

## Encoder Data Source API

### Updating an Encoder with a Data Source

This API function updates an Encoder with data from a data source.

#### URL

```
GET /symetry/rest/{cid}/encoders/{encodername}/learnds [body = DSInfo (encrypted)]
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                                                                               |
| ---------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED              | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request.   |
| **500**          | INTERNAL SERVER ERROR | If the server refuses to accept the new job, it notifies the client with the error "Job execution was refused by server." |

#### HTTP Response Entity

None

### Sample Request/Response

```
Request:
POST url="http://charm:8080/symetry/rest/c1/encoders/enctest/learnds

Request Body:
emUmJ7LXKaICPww/dKRoMZo4y++JZksKFU3xx80mZ0E83ED6eCXhZ7PR1jzizIQg/uH19jVkSUWnDxul9+NIqZe7a9Yz5a8gW1kpRHJ7SyOYoo7oV90atrkMcZ73Jj0FRn53P81t8Q+7fCTwMYRfD52hjCbwtvdwFCbPbhPo1c9CAk9QLhtAFLDWVqblBeBXTPR8/0zfKJWtwj30Yr0gwqeTI3+BMOMEvH28WFWXo+wBdgkMBpXJsQH/zbPvVdCp9P+BJWp/E1Ju2hUzPO5c2k3/Dmqv3xWNmsQzEJWNMFTJnBG33hTUyTr/+j87NsM2e1luWvf1KaNzaatjS1ZZ9AQCp7gv48vMVrzEHs4ePiOFq5t0UElAu1kzFerhtNxEFmC9A90Gjt3FSLrEGgx5emZ0uogJa6m4nufbQnKUrUZL1sILmLfReZOqnPKp59HqjiiczpkPQ2vPfhmFpaM2RYaEHbqSpMGCnBO9Axij9ExPjR4X9aqHOzKw2yBba4Em

Response Header:
Location: http://charm:8080/symetry/rest/c1/jobs/4

Response:
{"statusCode":"ACCEPTED","statusString":"Job Created","values":{}}

Job Request:
GET url="http://charm:8080/symetry/rest/c1/jobs/4

Job Response:
{"statusCode":"OK","statusString":"Job is finished","values":{}}
```

### Listing a Data Source Used by an Encoder

This API function lists the data source(s) that were used to update an Encoder.

#### URL

```
GET /symetry/rest/{cid}/encoders/{encodername}/dss
```

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                             |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **202**          | ACCEPTED            | Success. Includes an HTTP Location header specifying the location of the job ID that was created to handle the request. |

#### HTTP Response Entity

| HTTP Response Entity | Example                                                               |
| -------------------- | --------------------------------------------------------------------- |
| **StringList**       | See [StringList](appendix-a-json-data-structure-schema.md#stringlist) |

#### Sample Request/Response

```
Request:
GET url="http://charm:8080/symetry/rest/c1/encoders/enctest/dss

Response:
{"statusCode":"OK","statusString":"OK","values":{"stringList":{"values":["s12"]}}}
```

## Data Source Job Status

When invoking a JobStatus for a job that was initiated for learning, forgetting, or making predictions based on a data source, the response might contain an entity. The following sections describe these cases.

### Learning a Data Source

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                                                                                                                                                              |
| ---------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200**          | OK                  | No entity.                                                                                                                                                                                                                                                                                                                               |
| **202**          | ACCEPTED            | Job is not finished. Includes a [JobInfo](appendix-a-json-data-structure-schema.md#jobinfo) entity. Using the current and maximum fields, you can compute the percentage of the job that was accomplished so far. "Maximum" refers to the size of the file to process and contains the approximate number of bytes processed by the job. |

### Forgetting a Data Source

#### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **200**          | OK                  | No entity.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **202**          | ACCEPTED            | Job is not finished. Includes a JobInfo entity. For more information, see the section [JobInfo](appendix-a-json-data-structure-schema.md#jobinfo) and also the section on [Symetry Jobs](symetryml-job-information.md). Using the current and maximum fields, you can compute the percentage of the job that was accomplished so far. "Maximum" refers to the size of the file to process and contains the approximate number of bytes processed by the job. |

### Prediction Based on Data Source

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **200**          | OK                  | DataFrame that contains a sample of the predictions (up to 128 lines). Because Amazon files can be very large, it is not possible to return the prediction result file in its entirety within a REST call. Use your favorite tool to fetch the prediction results from the data source (S3 or SFTP). The prediction result file contains all the original file columns, plus the additional prediction column for each row. Any additional columns depend on the type of model used to make the predictions. |
| **202**          | ACCEPTED            | Job is not finished. Includes a [JobInfo](appendix-a-json-data-structure-schema.md#jobinfo) entity. Using the current and maximum fields, it is possible to compute the percentage of the job that was accomplished so far. "Maximum" refers to the size of the file to process and contains the approximate number of bytes processed by the job.                                                                                                                                                           |
