# COPY from Amazon EMR<a name="copy-parameters-data-source-emr"></a>

You can use the COPY command to load data in parallel from an Amazon EMR cluster configured to write text files to the cluster's Hadoop Distributed File System \(HDFS\) in the form of fixed\-width files, character\-delimited files, CSV files, JSON\-formatted files, or Avro files\.

**Topics**
+ [Syntax](#copy-parameters-data-source-emr-syntax)
+ [Example](#copy-parameters-data-source-emr-example)
+ [Parameters](#copy-parameters-data-source-emr-parameters)
+ [Supported parameters](#copy-parameters-data-source-emr-optional-parms)
+ [Unsupported parameters](#copy-parameters-data-source-emr-unsupported-parms)

## Syntax<a name="copy-parameters-data-source-emr-syntax"></a>

```
FROM 'emr://emr_cluster_id/hdfs_filepath'  
authorization
[ optional_parameters ]
```

## Example<a name="copy-parameters-data-source-emr-example"></a>

The following example loads data from as Amazon EMR cluster\. 

```
copy sales
from 'emr://j-SAMPLE2B500FC/myoutput/part-*' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

## Parameters<a name="copy-parameters-data-source-emr-parameters"></a>

FROM  
The source of the data to be loaded\. 

 'emr://*emr\_cluster\_id*/*hdfs\_file\_path*'  <a name="copy-emr"></a>
The unique identifier for the Amazon EMR cluster and the HDFS file path that references the data files for the COPY command\. The HDFS data file names must not contain the wildcard characters asterisk \(\*\) and question mark \(?\)\.   
The Amazon EMR cluster must continue running until the COPY operation completes\. If any of the HDFS data files are changed or deleted before the COPY operation completes, you might have unexpected results, or the COPY operation might fail\. 
You can use the wildcard characters asterisk \(\*\) and question mark \(?\) as part of the *hdfs\_file\_path* argument to specify multiple files to be loaded\. For example, `'emr://j-SAMPLE2B500FC/myoutput/part*'` identifies the files `part-0000`, `part-0001`, and so on\. If the file path doesn't contain wildcard characters, it is treated as a string literal\. If you specify only a folder name, COPY attempts to load all files in the folder\.   
If you use wildcard characters or use only the folder name, verify that no unwanted files will be loaded\. For example, some processes might write a log file to the output folder\.
For more information, see [Loading data from Amazon EMR](loading-data-from-emr.md)\.

*authorization*  
The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster \(role\-based access control\) or by providing the access credentials for an IAM user \(key\-based access control\)\. For increased security and flexibility, we recommend using IAM role\-based access control\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.

## Supported parameters<a name="copy-parameters-data-source-emr-optional-parms"></a>

You can optionally specify the following parameters with COPY from Amazon EMR: 
+ [Column mapping options](copy-parameters-column-mapping.md)
+ [Data format parameters](copy-parameters-data-format.md#copy-data-format-parameters)
+ [Data conversion parameters](copy-parameters-data-conversion.md)
+ [ Data load operations](copy-parameters-data-load.md)

## Unsupported parameters<a name="copy-parameters-data-source-emr-unsupported-parms"></a>

You can't use the following parameters with COPY from Amazon EMR: 
+ ENCRYPTED
+ MANIFEST
+ REGION
+ READRATIO
+ SSH