# Data Sources


## Creating New Data Sources

To create a new data source:

Open the **Data Sources** accordion and click the **Add Data** button. A New Data Source wizard appears.

![Data Sources: Select Add data button](<../../.gitbook/assets/create\_ds\_0.png>)

Select the preferred **Data Source Type**. The wizard forms change, depending on the type selected, as shown in the following figures

![S3 Data Source](../../.gitbook/assets/s3\_ds.png)

![SFTP Data Souce](../../.gitbook/assets/sftp\_ds.png)

![Local Data Source](../../.gitbook/assets/local\_ds.png)

![Redshift Data Source](../../.gitbook/assets/redshift\_ds.png)

![JDBC Data Source](../../.gitbook/assets/jdbc\_ds.png)

![HTTP Data Source](../../.gitbook/assets/http\_ds.png)

Ensure your data source settings are valid.

Click **Finish** to add the data source.

![Verify New Data Source](../../.gitbook/assets/verify\_new\_data\_source.png)

### Google Cloud Storage (GCS)

For **Data Source Type,** select Google Cloud Storage. Enter the GCS Project name (optional), _GCS Access Key_, _GCS Secret key_, and then navigate to your data file you wish to create a data source with. For more information on Google Access and Secret keys please see the Cloud Storage [HMAC](https://cloud.google.com/storage/docs/authentication/hmackeys) guide.

![GCS:  Enter account information](<../../.gitbook/assets/create\_ds\_gcs\_0.png>)

### Oracle Cloud Infrastructure (OCI)

Please consult the [Amazon S3 Compatibility API](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm) guide for details on how to configure your Oracle OCI Account for use with SymetryML.

For **Data Source Type,** select OCI. Enter OCI _Access Key_, _OCI Secret key_, OCI _Namespace_, OCI _Region_, and then navigate to your data file you wish to create a data source with.

![OCI: Enter account information](<../../.gitbook/assets/create\_ds\_oci\_0.png>)

### Azure Blob Storage (AZB)

For **Data Source Type,** select Azure Blob Storage.&#x20;

Once selecting for Azure, choose your authentication method, _SAS Token_, _Shared Token_ or _Authentication String_.

![Azure: Select Authentication Method](<../../.gitbook/assets/create\_ds\_azure\_0.png>)

## Spark Enabled Data Sources

Certain data sources are able to leverage Apache Spark for distributed learning and encoder creation. The list of data sources which are able to support Spark are:

* AWS S3
* Azure Blob Storage
* Oracle Object Storage
* Google Cloud Storage

To enable spark for one of the supported data source, one simply has to select the **Enable Spark** checkbox, choose the preferred version of Spark, and supply the master url.

![Spark Required Inputs](../../.gitbook/assets/spark\_0.png)

Additional Spark specific configurations can be supplied by clicking on the **Spark Options** button and supplying the corresponding key-value configuration pairs.

![Spark Optional Inputs](../../.gitbook/assets/spark\_1.png)

For a full list of spark specific configuration parameters please see the official spark documentation [page](https://spark.apache.org/docs/3.0.2/configuration.html)

## Uploading Data Source

Files local to the client’s machine can be uploaded onto a SFTP server via the Upload Wizard. This can be done by selecting a destination source and selecting a local file from the user’s computer.

![Upload File](../../.gitbook/assets/upload\_file.png)

## Viewing Data Sources

To view your newly created data source:

1. Double-click the data source node under the **Data Sources** accordion.
2. Inspect your newly added data.

![Data Source Preview](../../.gitbook/assets/data\_source\_preview.png)

## Deleting Data Sources

To delete a data source:

1. Right-click the **Data Source** node, and then click **Delete**.

![Data Source Delete](../../.gitbook/assets/data\_source\_delete.png)

## Editing Data Sources

To edit a data source:

1. Right-click the **Data Source** node, and then click **Edit**.
2. Update the fields as appropriate, and then click **Next** to continue.
3. After validating your data source, click **Finish** to commit the changes.

![Data Source Edit](../../.gitbook/assets/data\_source\_editing.png)
