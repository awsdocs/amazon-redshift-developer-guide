# COPY from Amazon S3<a name="copy-parameters-data-source-s3"></a>

To load data from files located in one or more S3 buckets, use the FROM clause to indicate how COPY locates the files in Amazon S3\. You can provide the object path to the data files as part of the FROM clause, or you can provide the location of a manifest file that contains a list of Amazon S3 object paths\. COPY from Amazon S3 uses an HTTPS connection\. 

**Important**  
If the Amazon S3 buckets that hold the data files don't reside in the same AWS Region as your cluster, you must use the [REGION](#copy-region) parameter to specify the Region in which the data is located\. 

**Topics**
+ [Syntax](#copy-parameters-data-source-s3-syntax)
+ [Examples](#copy-parameters-data-source-s3-examples)
+ [Optional parameters](#copy-parameters-data-source-s3-optional-parms)
+ [Unsupported parameters](#copy-parameters-data-source-s3-unsupported-parms)

## Syntax<a name="copy-parameters-data-source-s3-syntax"></a>

```
FROM { 's3://objectpath' | 's3://manifest_file' }
authorization
| MANIFEST
| ENCRYPTED
| REGION [AS] 'aws-region'
| optional-parameters
```

## Examples<a name="copy-parameters-data-source-s3-examples"></a>

The following example uses an object path to load data from Amazon S3\. 

```
copy customer
from 's3://mybucket/customer' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The following example uses a manifest file to load data from Amazon S3\. 

```
copy customer
from 's3://mybucket/cust.manifest' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest;
```

### Parameters<a name="copy-parameters-data-source-s3-parameters"></a>

FROM  <a name="copy-parameters-from"></a>
The source of the data to be loaded\. For more information about the encoding of the Amazon S3 file, see [Data conversion parameters](copy-parameters-data-conversion.md)\.

's3://*copy\_from\_s3\_objectpath*'   <a name="copy-s3-objectpath"></a>
Specifies the path to the Amazon S3 objects that contain the data—for example, `'s3://mybucket/custdata.txt'`\. The *s3://copy\_from\_s3\_objectpath* parameter can reference a single file or a set of objects or folders that have the same key prefix\. For example, the name `custdata.txt` is a key prefix that refers to a number of physical files: `custdata.txt`,`custdata.txt.1`, `custdata.txt.2`, `custdata.txt.bak`,and so on\. The key prefix can also reference a number of folders\. For example, `'s3://mybucket/custfolder'` refers to the folders `custfolder`, `custfolder_1`, `custfolder_2`, and so on\. If a key prefix references multiple folders, all of the files in the folders are loaded\. If a key prefix matches a file as well as a folder, such as `custfolder.log`, COPY attempts to load the file also\. If a key prefix might result in COPY attempting to load unwanted files, use a manifest file\. For more information, see [copy_from_s3_manifest_file](#copy-manifest-file), following\.   
If the S3 bucket that holds the data files doesn't reside in the same AWS Region as your cluster, you must use the [REGION](#copy-region) parameter to specify the Region in which the data is located\.
For more information, see [Loading data from Amazon S3](t_Loading-data-from-S3.md)\.

's3://*copy\_from\_s3\_manifest\_file*'   <a name="copy-manifest-file"></a>
Specifies the Amazon S3 object key for a manifest file that lists the data files to be loaded\. The *'s3://*copy\_from\_s3\_manifest\_file'** argument must explicitly reference a single file—for example, `'s3://mybucket/manifest.txt'`\. It can't reference a key prefix\.  
The manifest is a text file in JSON format that lists the URL of each file that is to be loaded from Amazon S3\. The URL includes the bucket name and full object path for the file\. The files that are specified in the manifest can be in different buckets, but all the buckets must be in the same AWS Region as the Amazon Redshift cluster\. If a file is listed twice, the file is loaded twice\. The following example shows the JSON for a manifest that loads three files\.   

```
{
  "entries": [
    {"url":"s3://mybucket-alpha/custdata.1","mandatory":true},
    {"url":"s3://mybucket-alpha/custdata.2","mandatory":true},
    {"url":"s3://mybucket-beta/custdata.1","mandatory":false}
  ]
}
```
The double quotation mark characters are required, and must be simple quotation marks \(0x22\), not slanted or "smart" quotation marks\. Each entry in the manifest can optionally include a `mandatory` flag\. If `mandatory` is set to `true`, COPY terminates if it doesn't find the file for that entry; otherwise, COPY will continue\. The default value for `mandatory` is `false`\.   
When loading from data files in ORC or Parquet format, a `meta` field is required, as shown in the following example\.  

```
{  
   "entries":[  
      {  
         "url":"s3://mybucket-alpha/orc/2013-10-04-custdata",
         "mandatory":true,
         "meta":{  
            "content_length":99
         }
      },
      {  
         "url":"s3://mybucket-beta/orc/2013-10-05-custdata",
         "mandatory":true,
         "meta":{  
            "content_length":99
         }
      }
   ]
}
```
The manifest file must not be encrypted or compressed, even if the ENCRYPTED, GZIP, LZOP, BZIP2, or ZSTD options are specified\. COPY returns an error if the specified manifest file isn't found or the manifest file isn't properly formed\.   
If a manifest file is used, the MANIFEST parameter must be specified with the COPY command\. If the MANIFEST parameter isn't specified, COPY assumes that the file specified with FROM is a data file\.   
For more information, see [Loading data from Amazon S3](t_Loading-data-from-S3.md)\.

*authorization*  
The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster \(role\-based access control\) or by providing the access credentials for an IAM user \(key\-based access control\)\. For increased security and flexibility, we recommend using IAM role\-based access control\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.

MANIFEST  <a name="copy-manifest"></a>
Specifies that a manifest is used to identify the data files to be loaded from Amazon S3\. If the MANIFEST parameter is used, COPY loads data from the files listed in the manifest referenced by *'s3://copy\_from\_s3\_manifest\_file'*\. If the manifest file isn't found, or isn't properly formed, COPY fails\. For more information, see [Using a manifest to specify data files](loading-data-files-using-manifest.md)\.

ENCRYPTED  <a name="copy-encrypted"></a>
A clause that specifies that the input files on Amazon S3 are encrypted using client\-side encryption with customer\-managed symmetric keys \(CSE\-CMK\)\. For more information, see [Loading encrypted data files from Amazon S3](c_loading-encrypted-files.md)\. Don't specify ENCRYPTED if the input files are encrypted using Amazon S3 server\-side encryption \(SSE\-KMS or SSE\-S3\)\. COPY reads server\-side encrypted files automatically\.  
If you specify the ENCRYPTED parameter, you must also specify the [MASTER_SYMMETRIC_KEY](#copy-master-symmetric-key) parameter or include the **master\_symmetric\_key** value in the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) string\.  
If the encrypted files are in compressed format, add the GZIP, LZOP, BZIP2, or ZSTD parameter\.  
Manifest files and JSONPaths files must not be encrypted, even if the ENCRYPTED option is specified\.

MASTER\_SYMMETRIC\_KEY '*master\_key*'  <a name="copy-master-symmetric-key"></a>
The master symmetric key that was used to encrypt data files on Amazon S3\. If MASTER\_SYMMETRIC\_KEY is specified, the [ENCRYPTED](#copy-encrypted) parameter must also be specified\. MASTER\_SYMMETRIC\_KEY can't be used with the CREDENTIALS parameter\. For more information, see [Loading encrypted data files from Amazon S3](c_loading-encrypted-files.md)\.  
If the encrypted files are in compressed format, add the GZIP, LZOP, BZIP2, or ZSTD parameter\.

REGION \[AS\] '*aws\-region*'  <a name="copy-region"></a>
Specifies the AWS Region where the source data is located\. REGION is required for COPY from an Amazon S3 bucket or an DynamoDB table when the AWS resource that contains the data isn't in the same Region as the Amazon Redshift cluster\.   
The value for *aws\_region* must match a Region listed in the [Amazon Redshift regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#redshift_region) table\.  
If the REGION parameter is specified, all resources, including a manifest file or multiple Amazon S3 buckets, must be located in the specified Region\.   
Transferring data across Regions incurs additional charges against the Amazon S3 bucket or the DynamoDB table that contains the data\. For more information about pricing, see **Data Transfer OUT From Amazon S3 To Another AWS Region** on the [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) page and **Data Transfer OUT** on the [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing/) page\. 
By default, COPY assumes that the data is located in the same Region as the Amazon Redshift cluster\. 

## Optional parameters<a name="copy-parameters-data-source-s3-optional-parms"></a>

You can optionally specify the following parameters with COPY from Amazon S3: 
+ [Column mapping options](copy-parameters-column-mapping.md)
+ [Data format parameters](copy-parameters-data-format.md#copy-data-format-parameters)
+ [Data conversion parameters](copy-parameters-data-conversion.md)
+ [ Data load operations](copy-parameters-data-load.md)

## Unsupported parameters<a name="copy-parameters-data-source-s3-unsupported-parms"></a>

You can't use the following parameters with COPY from Amazon S3: 
+ SSH
+ READRATIO