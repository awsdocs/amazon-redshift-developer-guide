# UNLOAD<a name="r_UNLOAD"></a>

Unloads the result of a query to one or more files on Amazon Simple Storage Service \(Amazon S3\), using Amazon S3 server\-side encryption \(SSE\-S3\)\. You can also specify server\-side encryption with an AWS Key Management Service key \(SSE\-KMS\) or client\-side encryption with a customer\-managed key \(CSE\-CMK\)\.

You can manage the size of files on Amazon S3, and, by extension, the number of files, by setting the MAXFILESIZE parameter\.

## Syntax<a name="r_UNLOAD-synopsis"></a>

```
UNLOAD ('select-statement')
TO 's3://object-path/name-prefix'
authorization
[ option [ ... ] ]

where option is

{ MANIFEST
| DELIMITER [ AS ] 'delimiter-char' 
| FIXEDWIDTH [ AS ] 'fixedwidth-spec' }  
| ENCRYPTED
| BZIP2  
| GZIP     
| ADDQUOTES 
| NULL [ AS ] 'null-string'
| ESCAPE
| ALLOWOVERWRITE
| PARALLEL [ { ON | TRUE } | { OFF | FALSE } ]
[ MAXFILESIZE [AS] max-size [ MB | GB ] ]
```

## Parameters<a name="unload-parameters"></a>

\('*select\-statement*'\)   
A SELECT query\. The results of the query are unloaded\. In most cases, it is worthwhile to unload data in sorted order by specifying an ORDER BY clause in the query; this approach saves the time required to sort the data when it is reloaded\.   
The query must be enclosed in single quotes as shown following:   

```
('select * from venue order by venueid')
```
If your query contains quotes \(enclosing literal values, for example\), or backslashes \(\\\), you need to escape them in the query text as shown following:   

```
('select * from venue where venuestate=\'NV\'')
```

TO 's3://*object\-path/name\-prefix*'   
The full path, including bucket name, to the location on Amazon S3 where Amazon Redshift will write the output file objects, including the manifest file if MANIFEST is specified\. The object names are prefixed with *name\-prefix*\. For added security, UNLOAD connects to Amazon S3 using an HTTPS connection\. By default, UNLOAD writes one or more files per slice\. UNLOAD appends a slice number and part number to the specified name prefix as follows:  
`<object-path>/<name-prefix><slice-number>_part_<part-number>`\.   
If MANIFEST is specified, the manifest file is written as follows:  
`<object_path>/<name_prefix>manifest`\.   
UNLOAD automatically creates encrypted files using Amazon S3 server\-side encryption \(SSE\), including the manifest file if MANIFEST is used\. The COPY command automatically reads server\-side encrypted files during the load operation\. You can transparently download server\-side encrypted files from your bucket using either the Amazon S3 management console or APIs\. For more information, go to [Protecting Data Using Server\-Side Encryption](http://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)\.   
To use Amazon S3 client\-side encryption, specify the ENCRYPTED option\.  
The Amazon S3 bucket where Amazon Redshift will write the output files must reside in the same region as your cluster\.

Authorization  
The UNLOAD command needs authorization to write data to Amazon S3\. The UNLOAD command uses the same parameters the COPY command uses for authorization\. For more information, see [Authorization Parameters](copy-parameters-authorization.md) in the COPY command syntax reference\.

MANIFEST  
Creates a manifest file that explicitly lists the data files that are created by the UNLOAD process\. The manifest is a text file in JSON format that lists the URL of each file that was written to Amazon S3\. The manifest file is written to the same Amazon S3 path prefix as the unload files in the format` <object_path_prefix>manifest`\. For example, if the UNLOAD specifies the Amazon S3 path prefix '`s3://mybucket/venue_`', the manifest file location will be '`s3://mybucket/venue_manifest`'\.

DELIMITER AS '*delimiter\_character*'   
Single ASCII character that is used to separate fields in the output file, such as a pipe character \( | \), a comma \( , \), or a tab \( \\t \)\. The default delimiter is a pipe character\. The AS keyword is optional\. DELIMITER can't be used with FIXEDWIDTH\. If the data contains the delimiter character, you will need to specify the ESCAPE option to escape the delimiter, or use ADDQUOTES to enclose the data in double quotes\. Alternatively, specify a delimiter that is not contained in the data\.

FIXEDWIDTH '*fixedwidth\_spec*'   
Unloads the data to a file where each column width is a fixed length, rather than separated by a delimiter\. The *fixedwidth\_spec* is a string that specifies the number of columns and the width of the columns\. The AS keyword is optional\. FIXEDWIDTH can't be used with DELIMITER\. Because FIXEDWIDTH does not truncate data, the specification for each column in the UNLOAD statement needs to be at least as long as the length of the longest entry for that column\. The format for *fixedwidth\_spec* is shown below:   

```
'colID1:colWidth1,colID2:colWidth2, ...'
```

ENCRYPTED  <a name="unload-parameters-encrypted"></a>
A clause that specifies that the output files on Amazon S3 will be encrypted using Amazon S3 server\-side encryption or client\-side encryption\. If MANIFEST is specified, the manifest file is also encrypted\. For more information, see [Unloading Encrypted Data Files](t_unloading_encrypted_files.md)\. If you don't specify the ENCRYPTED parameter, UNLOAD automatically creates encrypted files using Amazon S3 server\-side encryption with AWS\-managed encryption keys \(SSE\-S3\)\.   
To unload to Amazon S3 using server\-side encryption with an AWS KMS key \(SSE\-KMS\), use the [KMS_KEY_ID](#unload-parameters-kms-key-id) parameter to provide the key ID\. You can't use the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter with the KMS\_KEY\_ID parameter\. If you UNLOAD data using KMS\_KEY\_ID, you can then COPY the same data without specifying a key\.   
To unload to Amazon S3 using client\-side encryption with a customer\-supplied symmetric key \(CSE\-CMK\), provide the key using the [MASTER_SYMMETRIC_KEY](#unload-parameters-master-symmetric-key) parameter or the **master\_symmetric\_key** portion of a [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) credential string\. If you unload data using a master symmetric key, you must supply the same key when you COPY the encrypted data\.   
UNLOAD does not support Amazon S3 server\-side encryption with a customer\-supplied key \(SSE\-C\)\.   
To compress encrypted unload files, add the GZIP or BZIP2 parameter\. 

KMS\_KEY\_ID '*key\-id*'  <a name="unload-parameters-kms-key-id"></a>
The key ID for an AWS Key Management Service \(AWS KMS\) key to be used to encrypt data files on Amazon S3\. For more information, see [What is AWS Key Management Service?](http://docs.aws.amazon.com/kms/latest/developerguide/overview.html) If you specify KMS\_KEY\_ID, you must specify the [ENCRYPTED](#unload-parameters-encrypted) parameter also\. If you specify KMS\_KEY\_ID, you can't authenticate using the CREDENTIALS parameter\. Instead, use either [IAM_ROLE](copy-parameters-authorization.md#copy-iam-role) or [ACCESS_KEY_ID and SECRET_ACCESS_KEY](copy-parameters-authorization.md#copy-access-key-id)\. 

MASTER\_SYMMETRIC\_KEY '*master\_key*'  <a name="unload-parameters-master-symmetric-key"></a>
The master symmetric key to be used to encrypt data files on Amazon S3\. If you specify MASTER\_SYMMETRIC\_KEY, you must specify the [ENCRYPTED](#unload-parameters-encrypted) parameter also\. MASTER\_SYMMETRIC\_KEY can't be used with the CREDENTIALS parameter\. For more information, see [Loading Encrypted Data Files from Amazon S3](c_loading-encrypted-files.md)\. 

BZIP2   
Unloads data to one or more bzip2\-compressed files per slice\. Each resulting file is appended with a `.bz2` extension\. 

GZIP   
Unloads data to one or more gzip\-compressed files per slice\. Each resulting file is appended with a `.gz` extension\. 

ADDQUOTES   
Places quotation marks around each unloaded data field, so that Amazon Redshift can unload data values that contain the delimiter itself\. For example, if the delimiter is a comma, you could unload and reload the following data successfully:   

```
 "1","Hello, World" 
```
Without the added quotes, the string `Hello, World` would be parsed as two separate fields\.  
If you use ADDQUOTES, you must specify REMOVEQUOTES in the COPY if you reload the data\.

NULL AS '*null\-string*'   
Specifies a string that represents a null value in unload files\. If this option is used, all output files contain the specified string in place of any null values found in the selected data\. If this option is not specified, null values are unloaded as:   

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

+ A quote character: `"` or `'` \(if both ESCAPE and ADDQUOTES are specified in the UNLOAD command\)\.
If you loaded your data using a COPY with the ESCAPE option, you must also specify the ESCAPE option with your UNLOAD command to generate the reciprocal output file\. Similarly, if you UNLOAD using the ESCAPE option, you will need to use ESCAPE when you COPY the same data\.

ALLOWOVERWRITE   <a name="allowoverwrite"></a>
By default, UNLOAD fails if it finds files that it would possibly overwrite\. If ALLOWOVERWRITE is specified, UNLOAD will overwrite existing files, including the manifest file\. 

PARALLEL   <a name="unload-parallel"></a>
By default, UNLOAD writes data in parallel to multiple files, according to the number of slices in the cluster\. The default option is ON or TRUE\. If PARALLEL is OFF or FALSE, UNLOAD writes to one or more data files serially, sorted absolutely according to the ORDER BY clause, if one is used\. The maximum size for a data file is 6\.2 GB\. So, for example, if you unload 13\.4 GB of data, UNLOAD creates the following three files\.  

```
s3://mybucket/key000    6.2 GB
s3://mybucket/key001    6.2 GB
s3://mybucket/key002    1.0 GB
```
The UNLOAD command is designed to use parallel processing\. We recommend leaving PARALLEL enabled for most cases, especially if the files will be used to load tables using a COPY command\.

MAXFILESIZE AS max\-size \[ MB | GB \]   <a name="unload-maxfilesize"></a>
The maximum size of files UNLOAD creates in Amazon S3\. Specify a decimal value between 5 MB and 6\.2 GB\. The AS keyword is optional\. The default unit is MB\. If MAXFILESIZE is not specified, the default maximum file size is 6\.2 GB\. The size of the manifest file, if one is used, is not affected by MAXFILESIZE\.

## Usage Notes<a name="unload-usage-notes"></a>

### Using ESCAPE for All Delimited UNLOAD Operations<a name="unload-usage-escape"></a>

When you UNLOAD using a delimiter and there is any possibility that your data includes the delimiter or any of the characters listed in the ESCAPE option description, you must use the ESCAPE option with the UNLOAD statement\. If you do not use the ESCAPE option with the UNLOAD, subsequent COPY operations using the unloaded data might fail\.

**Important**  
We strongly recommend that you always use ESCAPE with both UNLOAD and COPY statements unless you are certain that your data does not contain any delimiters or other characters that might need to be escaped\. 

### Loss of Floating\-Point Precision<a name="unload-usage-floating-point-precision"></a>

You might encounter loss of precision for floating\-point data that is successively unloaded and reloaded\. 

### Limit Clause<a name="unload-usage-limit-clause"></a>

The SELECT query can't use a LIMIT clause in the outer SELECT\. For example, the following UNLOAD statement will fail:

```
unload ('select * from venue limit 10') 
to 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

Instead, use a nested LIMIT clause\. For example:

```
unload ('select * from venue where venueid in 
(select venueid from venue order by venueid desc limit 10)') 
to 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

Alternatively, you could populate a table using SELECT…INTO or CREATE TABLE AS using a LIMIT clause, then unload from that table\.

TOP is not currently support in UNLOAD statements\. Use LIMIT instead\.
