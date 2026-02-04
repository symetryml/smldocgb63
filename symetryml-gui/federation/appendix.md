# Appendix

## Appendix

### AWS - Federated Network

If AWS is chosen as the federation backend, The SymetryML federated learning network utilizes the following AWS services for transmission of information:

| AWS Service Used                      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS Simple Notification Service (SNS) | Topic and Subscriptions are used to disseminate messages across all the nodes in the federation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| AWS Simple Queuing Service (SQS)      | SQS queues are the final endpoint of SNS subscriptions and are used to store messages while waiting to be processed by federation nodes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| AWS Simple Storage Service (S3)       | When a node sends a scheduled message to other nodes in the federation, it will contain a pointer to a file that contain a serialized version of its SymetryML project. This file is stored on Amazon S3. Special care about where these files are stored need to be addressed. Typically your AWS admin would create a special IAM role for the AWS user credentials that will be used when creating / joining a federation. Ideally this IAM role should allow the user to read / write only a specific Amazon S3 bucket. See [About AWS S3 Access Rights](appendix.md#aws-s3-access-rights) for more details. |
| AWS Security Token Service (STS)      | This service is used to create temporary AWS credentials that will allow other nodes to read serialized SymetryML projects on S3. Again see [About AWS S3 Access Rights](appendix.md#aws-s3-access-rights) for more details.                                                                                                                                                                                                                                                                                                                                                                                     |

If your organization already uses AWS services, enabling federated learning is as simple as creating a set of AWS Credentials for various nodes in your network.

All that is needed to use SymetryML federated learning is to use an AWS user credential with the following access rights:

| AWS Policies Needed:                                     | Description                                                                                                                                                                    |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **AmazonSQSFullAccess**                                  | SQS queues are needed to store temporary messages sent to individual nodes                                                                                                     |
| **AmazonS3FullAccess** or some form of restricted access | S3 is needed to store periodic abstracts sent to other nodes. See [About AWS S3 Access Rights](appendix.md#aws-s3-access-rights) for details, and consult with your AWS admin. |
| **AmazonSNSFullAccess**                                  | SNS is needed to create a federation as well as connecting SQS queue to SNS topic.                                                                                             |

### AWS S3 Access Rights

The AWS credentials that are used when setting up a federation will be used to read and write files on Amazon S3. Ideally your AWS administrator should create a special role for the user credential being used. This is important as, even though SymetryML Fed ML will not send your credentials to other node(s) in the federation, it will use those credentials to create temporary Amazon STS credentials that will effectively have the same right as the credentials used when creating or joining a federation.

### HTTPS Support

In the case of a AWS backed federation, you will require each subsequent node in the federation to make a HTTP/HTTPS request to the initial admin node. This request is done automatically without any explicit action from the user. However, if the connection is made via HTTPS, one of the following adjustments must be made on the peer nodes.

#### **Option A:**

On the each peer node that will join the federation, add the federation administrator Jetty **SSL certificate** to the Jetty’s JVM trusted store. This can be done using the JVM `keytool -importcert` command.

#### **Option B:**

On each peer node that will join the federation, modify the `/opt/symetry/symetry-rest.txt` config file and append the following entry: `sml.fed.admin.trust_all_certs=1`. Note that this property will trust all unknown certification, so _**be extra careful**_ when using this option.
