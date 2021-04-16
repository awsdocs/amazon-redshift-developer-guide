# Data sources<a name="copy-parameters-data-source"></a>

You can load data from text files in an Amazon S3 bucket, in an Amazon EMR cluster, or on a remote host that your cluster can access using an SSH connection\. You can also load data directly from a DynamoDB table\. 

The maximum size of a single input row from any source is 4 MB\. 

To export data from a table to a set of files in an Amazon S3, use the [UNLOAD](r_UNLOAD.md) command\. 

**Topics**
+ [COPY from Amazon S3](copy-parameters-data-source-s3.md)
+ [COPY from Amazon EMR](copy-parameters-data-source-emr.md)
+ [COPY from remote host \(SSH\)](copy-parameters-data-source-ssh.md)
+ [COPY from Amazon DynamoDB](copy-parameters-data-source-dynamodb.md)