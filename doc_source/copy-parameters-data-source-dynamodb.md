# COPY from Amazon DynamoDB<a name="copy-parameters-data-source-dynamodb"></a>

To load data from an existing DynamoDB table, use the FROM clause to specify the DynamoDB table name\.

**Topics**
+ [Syntax](#copy-parameters-data-source-dynamodb-syntax)
+ [Examples](#copy-parameters-data-source-dynamodb-examples)
+ [Optional parameters](#copy-parameters-data-source-dynamodb-optional-parms)
+ [Unsupported parameters](#copy-parameters-data-source-dynamodb-unsupported-parms)

**Important**  
If the DynamoDB table doesn't reside in the same region as your Amazon Redshift cluster, you must use the REGION parameter to specify the region in which the data is located\. 

## Syntax<a name="copy-parameters-data-source-dynamodb-syntax"></a>

```
FROM 'dynamodb://table-name' 
authorization
READRATIO ratio
| REGION [AS] 'aws_region'  
| optional-parameters
```

## Examples<a name="copy-parameters-data-source-dynamodb-examples"></a>

The following example loads data from a DynamoDB table\. 

```
copy favoritemovies from 'dynamodb://ProductCatalog'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
readratio 50;
```

### Parameters<a name="copy-parameters-data-source-dynamodb-parameters"></a>

FROM  
The source of the data to be loaded\. 

'dynamodb://*table\-name*'  <a name="copy-dynamodb"></a>
The name of the DynamoDB table that contains the data, for example `'dynamodb://ProductCatalog'`\. For details about how DynamoDB attributes are mapped to Amazon Redshift columns, see [Loading data from an Amazon DynamoDB table](t_Loading-data-from-dynamodb.md)\.  
A DynamoDB table name is unique to an AWS account, which is identified by the AWS access credentials\.

*authorization*  
The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster \(role\-based access control\) or by providing the access credentials for an IAM user \(key\-based access control\)\. For increased security and flexibility, we recommend using IAM role\-based access control\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.

READRATIO \[AS\] *ratio*  <a name="copy-readratio"></a>
The percentage of the DynamoDB table's provisioned throughput to use for the data load\. READRATIO is required for COPY from DynamoDB\. It can't be used with COPY from Amazon S3\. We highly recommend setting the ratio to a value less than the average unused provisioned throughput\. Valid values are integers 1–200\.  
Setting READRATIO to 100 or higher enables Amazon Redshift to consume the entirety of the DynamoDB table's provisioned throughput, which seriously degrades the performance of concurrent read operations against the same table during the COPY session\. Write traffic is unaffected\. Values higher than 100 are allowed to troubleshoot rare scenarios when Amazon Redshift fails to fulfill the provisioned throughput of the table\. If you load data from DynamoDB to Amazon Redshift on an ongoing basis, consider organizing your DynamoDB tables as a time series to separate live traffic from the COPY operation\.

## Optional parameters<a name="copy-parameters-data-source-dynamodb-optional-parms"></a>

You can optionally specify the following parameters with COPY from Amazon DynamoDB: 
+ [Column mapping options](copy-parameters-column-mapping.md)
+ The following data conversion parameters are supported:
  + [ACCEPTANYDATE](copy-parameters-data-conversion.md#copy-acceptanydate) 
  + [BLANKSASNULL](copy-parameters-data-conversion.md#copy-blanksasnull) 
  + [DATEFORMAT](copy-parameters-data-conversion.md#copy-dateformat) 
  + [EMPTYASNULL](copy-parameters-data-conversion.md#copy-emptyasnull) 
  + [ROUNDEC](copy-parameters-data-conversion.md#copy-roundec) 
  + [TIMEFORMAT](copy-parameters-data-conversion.md#copy-timeformat) 
  + [TRIMBLANKS](copy-parameters-data-conversion.md#copy-trimblanks) 
  + [TRUNCATECOLUMNS](copy-parameters-data-conversion.md#copy-truncatecolumns) 
+ [ Data load operations](copy-parameters-data-load.md)

## Unsupported parameters<a name="copy-parameters-data-source-dynamodb-unsupported-parms"></a>

You can't use the following parameters with COPY from DynamoDB: 
+ All data format parameters
+ ESCAPE
+ FILLRECORD
+ IGNOREBLANKLINES
+ IGNOREHEADER
+ NULL
+ REMOVEQUOTES
+ ACCEPTINVCHARS
+ MANIFEST
+ ENCRYPTED