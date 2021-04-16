# Using the COPY command to load from Amazon S3<a name="t_loading-tables-from-s3"></a>

**Topics**
+ [Using a manifest to specify data files](loading-data-files-using-manifest.md)
+ [Loading compressed data files from Amazon S3](t_loading-gzip-compressed-data-files-from-S3.md)
+ [Loading fixed\-width data from Amazon S3](t_loading_fixed_width_data.md)
+ [Loading multibyte data from Amazon S3](t_loading_unicode_data.md)
+ [Loading encrypted data files from Amazon S3](c_loading-encrypted-files.md)

Use the [COPY](r_COPY.md) command to load a table in parallel from data files on Amazon S3\. You can specify the files to be loaded by using an Amazon S3 object prefix or by using a manifest file\.

The syntax to specify the files to be loaded by using a prefix is as follows:

```
copy <table_name> from 's3://<bucket_name>/<object_prefix>'
authorization;
```

 The manifest file is a JSON\-formatted file that lists the data files to be loaded\. The syntax to specify the files to be loaded by using a manifest file is as follows:

```
copy <table_name> from 's3://<bucket_name>/<manifest_file>'
authorization
manifest;
```

The table to be loaded must already exist in the database\. For information about creating a table, see [CREATE TABLE](r_CREATE_TABLE_NEW.md) in the SQL Reference\. 

The values for *authorization* provide the AWS authorization your cluster needs to access the Amazon S3 objects\. For information about required permissions, see [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\. The preferred method for authentication is to specify the IAM\_ROLE parameter and provide the Amazon Resource Name \(ARN\) for an IAM role with the necessary permissions\. Alternatively, you can specify the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters and provide the access key ID and secret access key for an authorized IAM user as plain text\. For more information, see [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based) or [Key\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based)\. 

To authenticate using the IAM\_ROLE parameter, replace *<aws\-account\-id>* and *<role\-name>* as shown in the following syntax\. 

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```

The following example shows authentication using an IAM role\.

```
copy customer 
from 's3://mybucket/mydata' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

To authenticate using IAM user credentials, replace *<access\-key\-id>* and *<secret\-access\-key* with an authorized user's access key ID and full secret access key for the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters as shown following\.

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>';
```

The following example shows authentication using IAM user credentials\.

```
copy customer 
from 's3://mybucket/mydata' 
access_key_id '<access-key-id>'
secret_access_key '<secret-access-key';
```

For more information about other authorization options, see [Authorization parameters](copy-parameters-authorization.md)

If you want to validate your data without actually loading the table, use the NOLOAD option with the [COPY](r_COPY.md) command\.

The following example shows the first few rows of a pipe\-delimited data in a file named `venue.txt`\.

```
1|Toyota Park|Bridgeview|IL|0
2|Columbus Crew Stadium|Columbus|OH|0
3|RFK Stadium|Washington|DC|0
```

Before uploading the file to Amazon S3, split the file into multiple files so that the COPY command can load it using parallel processing\. The number of files should be a multiple of the number of slices in your cluster\. Split your load data files so that the files are about equal size, between 1 MB and 1 GB after compression\. For more information, see [Splitting your data into multiple files](t_splitting-data-files.md)\.

For example, the `venue.txt` file might be split into four files, as follows:

```
venue.txt.1
venue.txt.2
venue.txt.3
venue.txt.4
```

The following COPY command loads the VENUE table using the pipe\-delimited data in the data files with the prefix 'venue' in the Amazon S3 bucket `mybucket`\. 

**Note**  
The Amazon S3 bucket `mybucket` in the following examples does not exist\. For sample COPY commands that use real data in an existing Amazon S3 bucket, see [ Step 4: Load sample data](cm-dev-t-load-sample-data.md)\.

```
copy venue from 's3://mybucket/venue'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|';
```

If no Amazon S3 objects with the key prefix 'venue' exist, the load fails\.