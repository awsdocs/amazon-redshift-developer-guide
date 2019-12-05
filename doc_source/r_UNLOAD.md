# UNLOAD<a name="r_UNLOAD"></a>

Unloads the result of a query to one or more text or Apache Parquet files on Amazon S3, using Amazon S3 server\-side encryption \(SSE\-S3\)\. You can also specify server\-side encryption with an AWS Key Management Service key \(SSE\-KMS\) or client\-side encryption with a customer\-managed key \(CSE\-CMK\)\.

You can manage the size of files on Amazon S3, and by extension the number of files, by setting the MAXFILESIZE parameter\.

You can unload the result of an Amazon Redshift query to your Amazon S3 data lake in Apache Parquet, an efficient open columnar storage format for analytics\. Parquet format is up to 2x faster to unload and consumes up to 6x less storage in Amazon S3, compared with text formats\. This enables you to save data transformation and enrichment you have done in Amazon S3 into your Amazon S3 data lake in an open format\. You can then analyze your data with Redshift Spectrum and other AWS services such as Amazon Athena, Amazon EMR, and Amazon SageMaker\. 

## Syntax<a name="r_UNLOAD-synopsis"></a>

```
UNLOAD ('select-statement')
TO 's3://object-path/name-prefix'
authorization
[ option [ ... ] ]

where option is
{ [ FORMAT [ AS ] ] CSV | PARQUET
| PARTITION BY ( column_name [, ... ] ) ]
| MANIFEST [ VERBOSE ] 
| HEADER           
| DELIMITER [ AS ] 'delimiter-char' 
| FIXEDWIDTH [ AS ] 'fixedwidth-spec'   
| ENCRYPTED
| BZIP2  
| GZIP 
| ZSTD
| ADDQUOTES 
| NULL [ AS ] 'null-string'
| ESCAPE
| ALLOWOVERWRITE
| PARALLEL [ { ON | TRUE } | { OFF | FALSE } ]
| MAXFILESIZE [AS] max-size [ MB | GB ] 
| REGION [AS] 'aws-region' }
```

## Parameters<a name="unload-parameters"></a>

\('*select\-statement*'\)   
A SELECT query\. The results of the query are unloaded\. In most cases, it is worthwhile to unload data in sorted order by specifying an ORDER BY clause in the query\. This approach saves the time required to sort the data when it is reloaded\.   
The query must be enclosed in single quotation marks as shown following:   

```
('select * from venue order by venueid')
```
If your query contains quotation marks \(for example to enclose literal values\), put the literal between two sets of single quotation marks—you must also enclose the query between single quotation marks:   

```
('select * from venue where venuestate=''NV''')
```

TO 's3://*object\-path/name\-prefix*'   
The full path, including bucket name, to the location on Amazon S3 where Amazon Redshift writes the output file objects, including the manifest file if MANIFEST is specified\. The object names are prefixed with *name\-prefix*\. If you use `PARTITION BY`, a forward slash \(/\) is automatically added to the end of the *name\-prefix* value if needed\. For added security, UNLOAD connects to Amazon S3 using an HTTPS connection\. By default, UNLOAD writes one or more files per slice\. UNLOAD appends a slice number and part number to the specified name prefix as follows:  
`<object-path>/<name-prefix><slice-number>_part_<part-number>`\.   
If MANIFEST is specified, the manifest file is written as follows:  
`<object_path>/<name_prefix>manifest`\.   
UNLOAD automatically creates encrypted files using Amazon S3 server\-side encryption \(SSE\), including the manifest file if MANIFEST is used\. The COPY command automatically reads server\-side encrypted files during the load operation\. You can transparently download server\-side encrypted files from your bucket using either the Amazon S3 Management Console or API\. For more information, go to [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)\.   
To use Amazon S3 client\-side encryption, specify the ENCRYPTED option\.  
REGION is required when the Amazon S3 bucket isn't in the same AWS Region as the Amazon Redshift cluster\. 

Authorization  
The UNLOAD command needs authorization to write data to Amazon S3\. The UNLOAD command uses the same parameters the COPY command uses for authorization\. For more information, see [Authorization Parameters](copy-parameters-authorization.md) in the COPY command syntax reference\.

\[ FORMAT \[AS\] \] CSV \| PARQUET  <a name="unload-csv"></a>
When CSV, unloads to a text file in CSV format using a comma \( , \) character as the delimiter\. If a field contains commas, double quotation marks, newline characters, or carriage returns, then the field in the unloaded file is enclosed in double quotation marks\. A double quotation mark within a data field is escaped by an additional double quotation mark\.   
When PARQUET, unloads to a file in Apache Parquet format\. By default, each row group is compressed using SNAPPY compression\. For more information about Apache Parquet format, see [Parquet](https://parquet.apache.org/)\.   
The FORMAT and AS keywords are optional\. You can't use CSV with DELIMITER or FIXEDWIDTH\. You can't use PARQUET with DELIMITER, FIXEDWIDTH, ADDQUOTES, ESCAPE, NULL AS, HEADER, GZIP, BZIP2, or ZSTD\. PARQUET with ENCRYPTED is only supported with server\-side encryption with an AWS Key Management Service key \(SSE\-KMS\)\.

PARTITON BY \( *column\_name* \[, \.\.\. \] \)   <a name="unload-partitionby"></a>
Specifies the partition keys for the unload operation\. UNLOAD automatically partitions output files into partition folders based on the partition key values, following the Apache Hive convention\. For example, a Parquet file that belongs to the partition year 2019 and the month September has the following prefix: `s3://my_bucket_name/my_prefix/year=2019/month=September/000.parquet`\.   
The value for *column\_name* must be a column in the query results being unloaded\. 

MANIFEST \[ VERBOSE \]  
Creates a manifest file that explicitly lists details for the data files that are created by the UNLOAD process\. The manifest is a text file in JSON format that lists the URL of each file that was written to Amazon S3\.   
If MANIFEST is specified with the VERBOSE option, the manifest includes the following details:   
+ The column names and data types, and for CHAR, VARCHAR, or NUMERIC data types, dimensions for each column\. For CHAR and VARCHAR data types, the dimension is the length\. For a DECIMAL or NUMERIC data type, the dimensions are precision and scale\. 
+ The row count unloaded to each file\. If the HEADER option is specified, the row count includes the header line\. 
+ The total file size of all files unloaded and the total row count unloaded to all files\. If the HEADER option is specified, the row count includes the header lines\. 
+ The author\. Author is always "Amazon Redshift"\.
You can specify VERBOSE only following MANIFEST\.   
The manifest file is written to the same Amazon S3 path prefix as the unload files in the format `<object_path_prefix>manifest`\. For example, if UNLOAD specifies the Amazon S3 path prefix '`s3://mybucket/venue_`', the manifest file location is '`s3://mybucket/venue_manifest`'\.

HEADER  
Adds a header line containing column names at the top of each output file\. Text transformation options, such as CSV, DELIMITER, ADDQUOTES, and ESCAPE, also apply to the header line\. HEADER can't be used with FIXEDWIDTH\.

DELIMITER AS '*delimiter\_character*'   
Single ASCII character that is used to separate fields in the output file, such as a pipe character \( \| \), a comma \( , \), or a tab \( \\t \)\. The default delimiter is a pipe character\. The AS keyword is optional\. DELIMITER can't be used with FIXEDWIDTH\. If the data contains the delimiter character, you need to specify the ESCAPE option to escape the delimiter, or use ADDQUOTES to enclose the data in double quotation marks\. Alternatively, specify a delimiter that isn't contained in the data\.

FIXEDWIDTH '*fixedwidth\_spec*'   
Unloads the data to a file where each column width is a fixed length, rather than separated by a delimiter\. The *fixedwidth\_spec* is a string that specifies the number of columns and the width of the columns\. The AS keyword is optional\. Because FIXEDWIDTH doesn't truncate data, the specification for each column in the UNLOAD statement needs to be at least as long as the length of the longest entry for that column\. The format for *fixedwidth\_spec* is shown below:   

```
'colID1:colWidth1,colID2:colWidth2, ...'
```
FIXEDWIDTH can't be used with DELIMITER or HEADER\.

ENCRYPTED  <a name="unload-parameters-encrypted"></a>
A clause that specifies that the output files on Amazon S3 are encrypted using Amazon S3 server\-side encryption or client\-side encryption\. If MANIFEST is specified, the manifest file is also encrypted\. For more information, see [Unloading Encrypted Data Files](t_unloading_encrypted_files.md)\. If you don't specify the ENCRYPTED parameter, UNLOAD automatically creates encrypted files using Amazon S3 server\-side encryption with AWS\-managed encryption keys \(SSE\-S3\)\.   
To unload to Amazon S3 using server\-side encryption with an AWS KMS key \(SSE\-KMS\), use the [KMS_KEY_ID](#unload-parameters-kms-key-id) parameter to provide the key ID\. You can't use the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter with the KMS\_KEY\_ID parameter\. If you UNLOAD data using KMS\_KEY\_ID, you can then COPY the same data without specifying a key\.   
To unload to Amazon S3 using client\-side encryption with a customer\-supplied symmetric key \(CSE\-CMK\), provide the key using the [MASTER_SYMMETRIC_KEY](#unload-parameters-master-symmetric-key) parameter or the **master\_symmetric\_key** portion of a [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) credential string\. If you unload data using a master symmetric key, you must supply the same key when you COPY the encrypted data\.   
UNLOAD doesn't support Amazon S3 server\-side encryption with a customer\-supplied key \(SSE\-C\)\.   
To compress encrypted unload files, add the BZIP2, GZIP, or ZSTD parameter\. 

KMS\_KEY\_ID '*key\-id*'  <a name="unload-parameters-kms-key-id"></a>
The key ID for an AWS Key Management Service \(AWS KMS\) key to be used to encrypt data files on Amazon S3\. For more information, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) If you specify KMS\_KEY\_ID, you must specify the [ENCRYPTED](#unload-parameters-encrypted) parameter also\. If you specify KMS\_KEY\_ID, you can't authenticate using the CREDENTIALS parameter\. Instead, use either [IAM_ROLE](copy-parameters-authorization.md#copy-iam-role) or [ACCESS_KEY_ID and SECRET_ACCESS_KEY](copy-parameters-authorization.md#copy-access-key-id)\. 

MASTER\_SYMMETRIC\_KEY '*master\_key*'  <a name="unload-parameters-master-symmetric-key"></a>
The master symmetric key to be used to encrypt data files on Amazon S3\. If you specify MASTER\_SYMMETRIC\_KEY, you must specify the [ENCRYPTED](#unload-parameters-encrypted) parameter also\. MASTER\_SYMMETRIC\_KEY can't be used with the CREDENTIALS parameter\. For more information, see [Loading Encrypted Data Files from Amazon S3](c_loading-encrypted-files.md)\. 

BZIP2   
Unloads data to one or more bzip2\-compressed files per slice\. Each resulting file is appended with a `.bz2` extension\. 

GZIP   
Unloads data to one or more gzip\-compressed files per slice\. Each resulting file is appended with a `.gz` extension\. 

ZSTD   
Unloads data to one or more Zstandard\-compressed files per slice\. Each resulting file is appended with a `.zst` extension\. 

ADDQUOTES   
Places quotation marks around each unloaded data field, so that Amazon Redshift can unload data values that contain the delimiter itself\. For example, if the delimiter is a comma, you could unload and reload the following data successfully:   

```
 "1","Hello, World" 
```
Without the added quotation marks, the string `Hello, World` would be parsed as two separate fields\.  
If you use ADDQUOTES, you must specify REMOVEQUOTES in the COPY if you reload the data\.

NULL AS '*null\-string*'   
Specifies a string that represents a null value in unload files\. If this option is used, all output files contain the specified string in place of any null values found in the selected data\. If this option isn't specified, null values are unloaded as:   
+ Zero\-length strings for delimited output 
+ Whitespace strings for fixed\-width output
If a null string is specified for a fixed\-width unload and the width of an output column is less than the width of the null string, the following behavior occurs:   
+ An empty field is output for non\-character columns 
+ An error is reported for character columns 

ESCAPE   
For CHAR and VARCHAR columns in delimited unload files, an escape character \(`\`\) is placed before every occurrence of the following characters:  
+ Linefeed: `\n`
+ Carriage return: `\r`
+ The delimiter character specified for the unloaded data\. 
+ The escape character: `\`
+ A quotation mark character: `"` or `'` \(if both ESCAPE and ADDQUOTES are specified in the UNLOAD command\)\.
If you loaded your data using a COPY with the ESCAPE option, you must also specify the ESCAPE option with your UNLOAD command to generate the reciprocal output file\. Similarly, if you UNLOAD using the ESCAPE option, you need to use ESCAPE when you COPY the same data\.

ALLOWOVERWRITE   <a name="allowoverwrite"></a>
By default, UNLOAD fails if it finds files that it would possibly overwrite\. If ALLOWOVERWRITE is specified, UNLOAD overwrites existing files, including the manifest file\. 

PARALLEL   <a name="unload-parallel"></a>
By default, UNLOAD writes data in parallel to multiple files, according to the number of slices in the cluster\. The default option is ON or TRUE\. If PARALLEL is OFF or FALSE, UNLOAD writes to one or more data files serially, sorted absolutely according to the ORDER BY clause, if one is used\. The maximum size for a data file is 6\.2 GB\. So, for example, if you unload 13\.4 GB of data, UNLOAD creates the following three files\.  

```
s3://mybucket/key000    6.2 GB
s3://mybucket/key001    6.2 GB
s3://mybucket/key002    1.0 GB
```
The UNLOAD command is designed to use parallel processing\. We recommend leaving PARALLEL enabled for most cases, especially if the files are used to load tables using a COPY command\.

MAXFILESIZE AS max\-size \[ MB \| GB \]   <a name="unload-maxfilesize"></a>
The maximum size of files UNLOAD creates in Amazon S3\. Specify a decimal value between 5 MB and 6\.2 GB\. The AS keyword is optional\. The default unit is MB\. If MAXFILESIZE isn't specified, the default maximum file size is 6\.2 GB\. The size of the manifest file, if one is used, isn't affected by MAXFILESIZE\.

REGION \[AS\] '*aws\-region*'  <a name="unload-region"></a>
The AWS Region where the target Amazon S3 bucket is located\. REGION is required for UNLOAD to an Amazon S3 bucket that isn't in the same AWS Region as the Amazon Redshift cluster\.   
The value for *aws\_region* must match an AWS Region listed in the [Amazon Redshift regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#redshift_region) table in the *AWS General Reference*\.  
By default, UNLOAD assumes that the target Amazon S3 bucket is located in the same AWS Region as the Amazon Redshift cluster\.

## Usage Notes<a name="unload-usage-notes"></a>

### Using ESCAPE for All Delimited Text UNLOAD Operations<a name="unload-usage-escape"></a>

When you UNLOAD using a delimiter, your data can include that delimiter or any of the characters listed in the ESCAPE option description\. In this case, you must use the ESCAPE option with the UNLOAD statement\. If you don't use the ESCAPE option with the UNLOAD, subsequent COPY operations using the unloaded data might fail\.

**Important**  
We strongly recommend that you always use ESCAPE with both UNLOAD and COPY statements\. The exception is if you are certain that your data doesn't contain any delimiters or other characters that might need to be escaped\. 

### Loss of Floating\-Point Precision<a name="unload-usage-floating-point-precision"></a>

You might encounter loss of precision for floating\-point data that is successively unloaded and reloaded\. 

### Limit Clause<a name="unload-usage-limit-clause"></a>

The SELECT query can't use a LIMIT clause in the outer SELECT\. For example, the following UNLOAD statement fails\.

```
unload ('select * from venue limit 10') 
to 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

Instead, use a nested LIMIT clause, as in the following example\.

```
unload ('select * from venue where venueid in 
(select venueid from venue order by venueid desc limit 10)') 
to 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

You can also populate a table using SELECT…INTO or CREATE TABLE AS using a LIMIT clause, then unload from that table\.

### Unloading a Column of the GEOMETRY Data Type<a name="unload-usage-geometry"></a>

You can only unload GEOMETRY columns to text or CSV format\. You can't unload GEOMETRY data with the `FIXEDWIDTH` option\. The data is unloaded in the hexadecimal form of the extended well\-known binary \(EWKB\) format\. If the size of the EWKB data is more than 4 MB, then a warning occurs because the data can't later be loaded into a table\. 

### FORMAT AS PARQUET Clause<a name="unload-parquet-usage"></a>

Be aware of these considerations when using FORMAT AS PARQUET:
+ Unload to Parquet doesn't use file level compression\. Each row group is compressed with SNAPPY\.
+ If MAXFILESIZE isn't specified, the default maximum file size is 6\.2 GB\. You can use MAXFILESIZE to specify a file size of 5 MB–6\.2 GB\. The actual file size is approximated when the file is being written, so it might not be exactly equal to the number you specify\.

  To maximize scan performance, Amazon Redshift tries to create Parquet files that contain equally sized 32\-MB row groups\. The MAXFILESIZE value that you specify is automatically rounded down to the nearest multiple of 32 MB\. For example, if you specify MAXFILESIZE 200 MB, then each Parquet file unloaded is approximately 192 MB \(32 MB row group x 6 = 192 MB\)\.
+ If a column uses TIMESTAMPTZ data format, only the timestamp values are unloaded\. The time zone information isn't unloaded\.
+ Don't specify file name prefixes that begin with underscore \(\_\) or period \(\.\) characters\. Redshift Spectrum treats files that begin with these characters as hidden files and ignores them\.

### PARTITION BY Clause<a name="unload-partitionby-usage"></a>

Be aware of these considerations when using PARTITION BY:
+ Partition columns aren't included in the output file\.
+ Make sure to include partition columns in the SELECT query used in the UNLOAD statement\. You can specify any number of partition columns in the UNLOAD command\. However, there is a limitation that there should be at least one nonpartition column to be part of the file\.
+ If the partition key value is null, Amazon Redshift automatically unloads that data into a default partition called `partition_column=__HIVE_DEFAULT_PARTITION__`\. 
+ The UNLOAD command doesn't make any calls to an external catalog\. To register your new partitions to be part of your existing external table, use a separate ALTER TABLE \.\.\. ADD PARTITION \.\.\. command\. Or you can run a CREATE EXTERNAL TABLE command to register the unloaded data as a new external table\. You can also use an AWS Glue crawler to populate your Data Catalog\. For more information, see [Defining Crawlers](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html) in the *AWS Glue Developer Guide*\. 
+ The MANIFEST keyword isn't supported\.
+ The column data types that you can use as the partition key are SMALLINT, INTEGER, BIGINT, DECIMAL, REAL, BOOLEAN, CHAR, VARCHAR, DATE, and TIMESTAM 
