# Step 5: Run the COPY commands<a name="tutorial-loading-run-copy"></a>

You run COPY commands to load each of the tables in the SSB schema\. The COPY command examples demonstrate loading from different file formats, using several COPY command options, and troubleshooting load errors\.

**Topics**
+ [COPY command syntax](#tutorial-loading-data-copy-syntax)
+ [Loading the SSB tables](#tutorial-loading-run-copy-load-tables)

## COPY command syntax<a name="tutorial-loading-data-copy-syntax"></a>

The basic [COPY](r_COPY.md) command syntax is as follows\. 

```
COPY table_name [ column_list ] FROM data_source CREDENTIALS access_credentials [options] 
```

To execute a COPY command, you provide the following values\. 
<a name="tutorial-loading-syntax-table-name"></a>
**Table name**  
The target table for the COPY command\. The table must already exist in the database\. The table can be temporary or persistent\. The COPY command appends the new input data to any existing rows in the table\. 
<a name="tutorial-loading-syntax-column-list"></a>
**Column list**  
By default, COPY loads fields from the source data to the table columns in order\. You can optionally specify a *column list,* that is a comma\-separated list of column names, to map data fields to specific columns\. You don't use column lists in this tutorial\. For more information, see [Column List](copy-parameters-column-mapping.md#copy-column-list) in the COPY command reference\.

<a name="tutorial-loading-syntax-data-source.title"></a>Data source

You can use the COPY command to load data from an Amazon S3 bucket, an Amazon EMR cluster, a remote host using an SSH connection, or an Amazon DynamoDB table\. For this tutorial, you load from data files in an Amazon S3 bucket\. When loading from Amazon S3, you must provide the name of the bucket and the location of the data files\. To do this, provide either an object path for the data files or the location of a manifest file that explicitly lists each data file and its location\. 
+ Key prefix 

  An object stored in Amazon S3 is uniquely identified by an object key, which includes the bucket name, folder names, if any, and the object name\. A *key prefix *refers to a set of objects with the same prefix\. The object path is a key prefix that the COPY command uses to load all objects that share the key prefix\. For example, the key prefix `custdata.txt` can refer to a single file or to a set of files, including `custdata.txt.001`, `custdata.txt.002`, and so on\. 
+ Manifest file

  In some cases, you might need to load files with different prefixes, for example from multiple buckets or folders\. In others, you might need to exclude files that share a prefix\. In these cases, you can use a manifest file\. A *manifest file* explicitly lists each load file and its unique object key\. You use a manifest file to load the PART table later in this tutorial\. 
<a name="tutorial-loading-syntax-credentials"></a>
**Credentials**  
To access the AWS resources that contain the data to load, you must provide AWS access credentials for an AWS user or an IAM user with sufficient privileges\. These credentials are an access key ID and a secret access key\. To load data from Amazon S3, the credentials must include ListBucket and GetObject permissions\. Additional credentials are required if your data is encrypted or if you are using temporary access credentials\. For more information, see [Authorization parameters](copy-parameters-authorization.md) in the COPY command reference\. For more information about managing access, go to [Managing access permissions to your Amazon S3 resources](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html)\. If you do not have an access key ID and secret access key, you need to get them\. For more information, go to [Administering access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html)\. 

<a name="tutorial-loading-syntax-options.title"></a>Options

You can specify a number of parameters with the COPY command to specify file formats, manage data formats, manage errors, and control other features\. In this tutorial, you use the following COPY command options and features: 
+ Key prefix

  For information on how to load from multiple files by specifying a key prefix, see [Load the PART table using NULL AS](#tutorial-loading-load-part)\.
+ CSV format

  For information on how to load data that is in CSV format, see [Load the PART table using NULL AS](#tutorial-loading-load-part)\.
+ NULL AS

  For information on how to load PART using the NULL AS option, see [Load the PART table using NULL AS](#tutorial-loading-load-part)\.
+ Character\-delimited format

  For information on how to use the DELIMITER option, see [Load the SUPPLIER table using REGION](#tutorial-loading-load-supplier)\.
+ REGION

  For information on how to use the REGION option, see [Load the SUPPLIER table using REGION](#tutorial-loading-load-supplier)\.
+ Fixed\-format width

  For information on how to load the CUSTOMER table from fixed\-width data, see [Load the CUSTOMER table using MANIFEST](#tutorial-loading-load-customer)\.
+ MAXERROR

  For information on how to use the MAXERROR option, see [Load the CUSTOMER table using MANIFEST](#tutorial-loading-load-customer)\.
+ ACCEPTINVCHARS

  For information on how to use the ACCEPTINVCHARS option, see [Load the CUSTOMER table using MANIFEST](#tutorial-loading-load-customer)\.
+ MANIFEST

  For information on how to use the MANIFEST option, see [Load the CUSTOMER table using MANIFEST](#tutorial-loading-load-customer)\.
+ DATEFORMAT

  For information on how to use the DATEFORMAT option, see [Load the DWDATE table using DATEFORMAT](#tutorial-loading-load-dwdate)\.
+ GZIP, LZOP and BZIP2

  For information on how to compress your files, see [Load the LINEORDER table using multiple files](#tutorial-loading-load-lineorder)\.
+ COMPUPDATE

  For information on how to use the COMPUPDATE option, see [Load the LINEORDER table using multiple files](#tutorial-loading-load-lineorder)\.
+ Multiple files

  For information on how to load multiple files, see [Load the LINEORDER table using multiple files](#tutorial-loading-load-lineorder)\.

## Loading the SSB tables<a name="tutorial-loading-run-copy-load-tables"></a>

You use the following COPY commands to load each of the tables in the SSB schema\. The command to each table demonstrates different COPY options and troubleshooting techniques\.

To load the SSB tables, follow these steps: 

1. [Replace the bucket name and AWS credentials](#tutorial-loading-run-copy-replaceables)

1. [Load the PART table using NULL AS](#tutorial-loading-load-part)

1. [Load the SUPPLIER table using REGION](#tutorial-loading-load-supplier)

1. [Load the CUSTOMER table using MANIFEST](#tutorial-loading-load-customer)

1. [Load the DWDATE table using DATEFORMAT](#tutorial-loading-load-dwdate)

1. [Load the LINEORDER table using multiple files](#tutorial-loading-load-lineorder)

### Replace the bucket name and AWS credentials<a name="tutorial-loading-run-copy-replaceables"></a>

The COPY commands in this tutorial are presented in the following format\.

```
copy table from 's3://<your-bucket-name>/load/key_prefix' 
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
options;
```

For each COPY command, do the following:

1. Replace *<your\-bucket\-name>* with the name of a bucket in the same region as your cluster\. 

   This step assumes the bucket and the cluster are in the same region\. Alternatively, you can specify the region using the [REGION](copy-parameters-data-source-s3.md#copy-region) option with the COPY command\. 

1. Replace *<Your\-Access\-Key\-ID>* and *<Your\-Secret\-Access\-Key>* with your own AWS IAM account credentials\. The segment of the credentials string that is enclosed in single quotation marks must not contain any spaces or line breaks\. 

### Load the PART table using NULL AS<a name="tutorial-loading-load-part"></a>

In this step, you use the CSV and NULL AS options to load the PART table\. 

The COPY command can load data from multiple files in parallel, which is much faster than loading from a single file\. To demonstrate this principle, the data for each table in this tutorial is split into eight files, even though the files are very small\. In a later step, you compare the time difference between loading from a single file and loading from multiple files\. For more information, see [Split your load data into multiple files](c_best-practices-use-multiple-files.md)\. 
<a name="tutorial-loading-key-prefix"></a>
**Key prefix**  
You can load from multiple files by specifying a key prefix for the file set, or by explicitly listing the files in a manifest file\. In this step, you use a key prefix\. In a later step, you use a manifest file\. The key prefix `'s3://mybucket/load/part-csv.tbl'` loads the following set of the files in the `load` folder\. 

```
part-csv.tbl-000
part-csv.tbl-001
part-csv.tbl-002
part-csv.tbl-003
part-csv.tbl-004
part-csv.tbl-005
part-csv.tbl-006
part-csv.tbl-007
```
<a name="tutorial-loading-csv-format"></a>
**CSV format**  
CSV, which stands for comma separated values, is a common format used for importing and exporting spreadsheet data\. CSV is more flexible than comma\-delimited format because it enables you to include quoted strings within fields\. The default quotation mark character for COPY from CSV format is a double quotation mark \( " \), but you can specify another quotation mark character by using the QUOTE AS option\. When you use the quotation mark character within the field, escape the character with an additional quotation mark character\.

The following excerpt from a CSV\-formatted data file for the PART table shows strings enclosed in double quotation marks \(`"LARGE ANODIZED BRASS"`\)\. It also shows a string enclosed in two double quotation marks within a quoted string \(`"MEDIUM ""BURNISHED"" TIN"`\)\.

```
15,dark sky,MFGR#3,MFGR#47,MFGR#3438,indigo,"LARGE ANODIZED BRASS",45,LG CASE
22,floral beige,MFGR#4,MFGR#44,MFGR#4421,medium,"PROMO, POLISHED BRASS",19,LG DRUM
23,bisque slate,MFGR#4,MFGR#41,MFGR#4137,firebrick,"MEDIUM ""BURNISHED"" TIN",42,JUMBO JAR
```

The data for the PART table contains characters that cause COPY to fail\. In this exercise, you troubleshoot the errors and correct them\. 

To load data that is in CSV format, add `csv` to your COPY command\. Execute the following command to load the PART table\. 

```
copy part from 's3://<your-bucket-name>/load/part-csv.tbl' 
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
csv;
```

You should get an error message similar to the following\.

```
An error occurred when executing the SQL command:
copy part from 's3://mybucket/load/part-csv.tbl' 
credentials' ...

ERROR: Load into table 'part' failed.  Check 'stl_load_errors' system table for details. [SQL State=XX000] 

Execution time: 1.46s

1 statement(s) failed.
1 statement(s) failed.
```

To get more information about the error, query the STL\_LOAD\_ERRORS table\. The following query uses the SUBSTRING function to shorten columns for readability and uses LIMIT 10 to reduce the number of rows returned\. You can adjust the values in `substring(filename,22,25)` to allow for the length of your bucket name\.

```
select query, substring(filename,22,25) as filename,line_number as line, 
substring(colname,0,12) as column, type, position as pos, substring(raw_line,0,30) as line_text,
substring(raw_field_value,0,15) as field_text, 
substring(err_reason,0,45) as reason
from stl_load_errors 
order by query desc
limit 10;
```

```
 query  |    filename      | line |  column   |    type    | pos |      
--------+-------------------------+-----------+------------+------------+-----+----
 333765 | part-csv.tbl-000 |    1 |           |            |   0 |

 line_text        | field_text |                    reason
------------------+------------+----------------------------------------------
 15,NUL next,     |            | Missing newline: Unexpected character 0x2c f
```
<a name="tutorial-loading-null-as"></a>
**NULL AS**  
The `part-csv.tbl` data files use the NUL terminator character \(`\x000` or `\x0`\) to indicate NULL values\.

**Note**  
Despite very similar spelling, NUL and NULL are not the same\. NUL is a UTF\-8 character with codepoint `x000` that is often used to indicate end of record \(EOR\)\. NULL is a SQL value that represents an absence of data\. 

By default, COPY treats a NUL terminator character as an EOR character and terminates the record, which often results in unexpected results or an error\. There is no single standard method of indicating NULL in text data\. Thus, the NULL AS COPY command option enables you to specify which character to substitute with NULL when loading the table\. In this example, you want COPY to treat the NUL terminator character as a NULL value\.

**Note**  
The table column that receives the NULL value must be configured as *nullable\.* That is, it must not include the NOT NULL constraint in the CREATE TABLE specification\.

To load PART using the NULL AS option, execute the following COPY command\.

```
copy part from 's3://<your-bucket-name>/load/part-csv.tbl' 
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
csv
null as '\000';
```

To verify that COPY loaded NULL values, execute the following command to select only the rows that contain NULL\.

```
select p_partkey, p_name, p_mfgr, p_category from part where p_mfgr is null;
```

```
 p_partkey |  p_name  | p_mfgr | p_category
-----------+----------+--------+------------
        15 | NUL next |        | MFGR#47
        81 | NUL next |        | MFGR#23
       133 | NUL next |        | MFGR#44 
(2 rows)
```

### Load the SUPPLIER table using REGION<a name="tutorial-loading-load-supplier"></a>

In this step, you use the DELIMITER and REGION options to load the SUPPLIER table\.

**Note**  
The files for loading the SUPPLIER table are provided in an AWS sample bucket\. You don't need to upload files for this step\.
<a name="tutorial-loading-character-delimited-format"></a>
**Character\-Delimited Format**  
The fields in a character\-delimited file are separated by a specific character, such as a pipe character \( \| \), a comma \( , \) or a tab \( \\t \)\. Character\-delimited files can use any single ASCII character, including one of the nonprinting ASCII characters, as the delimiter\. You specify the delimiter character by using the DELIMITER option\. The default delimiter is a pipe character \( \| \)\. 

The following excerpt from the data for the SUPPLIER table uses pipe\-delimited format\. 

```
1|1|257368|465569|41365|19950218|2-HIGH|0|17|2608718|9783671|4|2504369|92072|2|19950331|TRUCK
1|2|257368|201928|8146|19950218|2-HIGH|0|36|6587676|9783671|9|5994785|109794|6|19950416|MAIL
```
<a name="tutorial-loading-region"></a>
**REGION**  
Whenever possible, you should locate your load data in the same AWS region as your Amazon Redshift cluster\. If your data and your cluster are in the same region, you reduce latency, minimize eventual consistency issues, and avoid cross\-region data transfer costs\. For more information, see [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md) 

If you must load data from a different AWS region, use the REGION option to specify the AWS region in which the load data is located\. If you specify a region, all of the load data, including manifest files, must be in the named region\. For more information, see [REGION](copy-parameters-data-source-s3.md#copy-region)\. 

If your cluster is in the US East \(N\. Virginia\) region, execute the following command to load the SUPPLIER table from pipe\-delimited data in an Amazon S3 bucket located in the US West \(Oregon\) region\. For this example, do not change the bucket name\. 

```
copy supplier from 's3://awssampledbuswest2/ssbgz/supplier.tbl' 
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
delimiter '|' 
gzip
region 'us-west-2';
```

If your cluster is *not* in the US East \(N\. Virginia\) region, execute the following command to load the SUPPLIER table from pipe\-delimited data in an Amazon S3 bucket located in the US East \(N\. Virginia\) region\. For this example, do not change the bucket name\.

```
copy supplier from 's3://awssampledb/ssbgz/supplier.tbl' 
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
delimiter '|' 
gzip
region 'us-east-1';
```

### Load the CUSTOMER table using MANIFEST<a name="tutorial-loading-load-customer"></a>

In this step, you use the FIXEDWIDTH, MAXERROR, ACCEPTINVCHARS, and MANIFEST options to load the CUSTOMER table\.

The sample data for this exercise contains characters that cause errors when COPY attempts to load them\. You use the MAXERRORS option and the STL\_LOAD\_ERRORS system table to troubleshoot the load errors and then use the ACCEPTINVCHARS and MANIFEST options to eliminate the errors\.
<a name="tutorial-loading-fixed-width"></a>
**Fixed\-Width Format**  
Fixed\-width format defines each field as a fixed number of characters, rather than separating fields with a delimiter\. The following excerpt from the data for the CUSTOMER table uses fixed\-width format\.

```
1   Customer#000000001   IVhzIApeRb           MOROCCO  0MOROCCO  AFRICA      25-705 
2   Customer#000000002   XSTf4,NCwDVaWNe6tE   JORDAN   6JORDAN   MIDDLE EAST 23-453
3   Customer#000000003   MG9kdTD              ARGENTINA5ARGENTINAAMERICA     11-783
```

The order of the label/width pairs must match the order of the table columns exactly\. For more information, see [FIXEDWIDTH](copy-parameters-data-format.md#copy-fixedwidth)\.

The fixed\-width specification string for the CUSTOMER table data is as follows\.

```
fixedwidth 'c_custkey:10, c_name:25, c_address:25, c_city:10, c_nation:15, 
c_region :12, c_phone:15,c_mktsegment:10'
```

To load the CUSTOMER table from fixed\-width data, execute the following command\.

```
copy customer
from 's3://<your-bucket-name>/load/customer-fw.tbl'
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
fixedwidth 'c_custkey:10, c_name:25, c_address:25, c_city:10, c_nation:15, c_region :12, c_phone:15,c_mktsegment:10';
```

You should get an error message, similar to the following\.

```
An error occurred when executing the SQL command:
copy customer
from 's3://mybucket/load/customer-fw.tbl'
credentials'aws_access_key_id=...

ERROR: Load into table 'customer' failed.  Check 'stl_load_errors' system table for details. [SQL State=XX000] 

Execution time: 2.95s

1 statement(s) failed.
```
<a name="tutorial-loading-maxerror"></a>
**MAXERROR**  
By default, the first time COPY encounters an error, the command fails and returns an error message\. To save time during testing, you can use the MAXERROR option to instruct COPY to skip a specified number of errors before it fails\. Because we expect errors the first time we test loading the CUSTOMER table data, add `maxerror 10` to the COPY command\. 

To test using the FIXEDWIDTH and MAXERROR options, execute the following command\.

```
copy customer
from 's3://<your-bucket-name>/load/customer-fw.tbl'
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
fixedwidth 'c_custkey:10, c_name:25, c_address:25, c_city:10, c_nation:15, c_region :12, c_phone:15,c_mktsegment:10'
maxerror 10;
```

This time, instead of an error message, you get a warning message similar to the following\.

```
Warnings:
Load into table 'customer' completed, 112497 record(s) loaded successfully.
Load into table 'customer' completed, 7 record(s) could not be loaded.  Check 'stl_load_errors' system table for details.
```

The warning indicates that COPY encountered seven errors\. To check the errors, query the STL\_LOAD\_ERRORS table, as shown in the following example\.

```
select query, substring(filename,22,25) as filename,line_number as line, 
substring(colname,0,12) as column, type, position as pos, substring(raw_line,0,30) as line_text,
substring(raw_field_value,0,15) as field_text, 
substring(err_reason,0,45) as error_reason
from stl_load_errors 
order by query desc, filename 
limit 7;
```

The results of the STL\_LOAD\_ERRORS query should look similar to the following\.

```
 query  |         filename          | line |  column   |    type    | pos |           line_text           | field_text |              error_reason
--------+---------------------------+------+-----------+------------+-----+-------------------------------+------------+----------------------------------------------
 334489 | customer-fw.tbl.log       |    2 | c_custkey | int4       |  -1 | customer-fw.tbl               | customer-f | Invalid digit, Value 'c', Pos 0, Type: Integ
 334489 | customer-fw.tbl.log       |    6 | c_custkey | int4       |  -1 | Complete                      | Complete   | Invalid digit, Value 'C', Pos 0, Type: Integ
 334489 | customer-fw.tbl.log       |    3 | c_custkey | int4       |  -1 | #Total rows                   | #Total row | Invalid digit, Value '#', Pos 0, Type: Integ
 334489 | customer-fw.tbl.log       |    5 | c_custkey | int4       |  -1 | #Status                       | #Status    | Invalid digit, Value '#', Pos 0, Type: Integ
 334489 | customer-fw.tbl.log       |    1 | c_custkey | int4       |  -1 | #Load file                    | #Load file | Invalid digit, Value '#', Pos 0, Type: Integ
 334489 | customer-fw.tbl000        |    1 | c_address | varchar    |  34 | 1         Customer#000000001  | .Mayag.ezR | String contains invalid or unsupported UTF8
 334489 | customer-fw.tbl000        |    1 | c_address | varchar    |  34 | 1         Customer#000000001  | .Mayag.ezR | String contains invalid or unsupported UTF8
(7 rows)
```

By examining the results, you can see that there are two messages in the `error_reasons` column:
+ 

  ```
  Invalid digit, Value '#', Pos 0, Type: Integ 
  ```

  These errors are caused by the `customer-fw.tbl.log` file\. The problem is that it is a log file, not a data file, and should not be loaded\. You can use a manifest file to avoid loading the wrong file\. 
+ 

  ```
  String contains invalid or unsupported UTF8 
  ```

  The VARCHAR data type supports multibyte UTF\-8 characters up to three bytes\. If the load data contains unsupported or invalid characters, you can use the ACCEPTINVCHARS option to replace each invalid character with a specified alternative character\.

Another problem with the load is more difficult to detect—the load produced unexpected results\. To investigate this problem, execute the following command to query the CUSTOMER table\.

```
select c_custkey, c_name, c_address        
from customer
order by c_custkey
limit 10;
```

```
 c_custkey |          c_name           |         c_address
-----------+---------------------------+---------------------------
         2 | Customer#000000002        | XSTf4,NCwDVaWNe6tE
         2 | Customer#000000002        | XSTf4,NCwDVaWNe6tE
         3 | Customer#000000003        | MG9kdTD
         3 | Customer#000000003        | MG9kdTD
         4 | Customer#000000004        | XxVSJsL
         4 | Customer#000000004        | XxVSJsL
         5 | Customer#000000005        | KvpyuHCplrB84WgAi
         5 | Customer#000000005        | KvpyuHCplrB84WgAi
         6 | Customer#000000006        | sKZz0CsnMD7mp4Xd0YrBvx
         6 | Customer#000000006        | sKZz0CsnMD7mp4Xd0YrBvx
(10 rows)
```

The rows should be unique, but there are duplicates\. 

Another way to check for unexpected results is to verify the number of rows that were loaded\. In our case, 100000 rows should have been loaded, but the load message reported loading 112497 records\. The extra rows were loaded because the COPY loaded an extraneous file, `customer-fw.tbl0000.bak`\. 

In this exercise, you use a manifest file to avoid loading the wrong files\. 
<a name="tutorial-loading-acceptinvchars"></a>
**ACCEPTINVCHARS**  
By default, when COPY encounters a character that is not supported by the column's data type, it skips the row and returns an error\. For information about invalid UTF\-8 characters, see [Multibyte character load errors](multi-byte-character-load-errors.md)\. 

You could use the MAXERRORS option to ignore errors and continue loading, then query STL\_LOAD\_ERRORS to locate the invalid characters, and then fix the data files\. However, MAXERRORS is best used for troubleshooting load problems and should generally not be used in a production environment\. 

The ACCEPTINVCHARS option is usually a better choice for managing invalid characters\. ACCEPTINVCHARS instructs COPY to replace each invalid character with a specified valid character and continue with the load operation\. You can specify any valid ASCII character, except NULL, as the replacement character\. The default replacement character is a question mark \( ? \)\. COPY replaces multibyte characters with a replacement string of equal length\. For example, a 4\-byte character would be replaced with `'????'`\. 

COPY returns the number of rows that contained invalid UTF\-8 characters\. It also adds an entry to the STL\_REPLACEMENTS system table for each affected row, up to a maximum of 100 rows per node slice\. Additional invalid UTF\-8 characters are also replaced, but those replacement events are not recorded\. 

ACCEPTINVCHARS is valid only for VARCHAR columns\. 

For this step, you add the ACCEPTINVCHARS with the replacement character `'^'`\. 
<a name="tutorial-loading-manifest"></a>
**MANIFEST**  
When you COPY from Amazon S3 using a key prefix, there is a risk that you might load unwanted tables\. For example, the `'s3://mybucket/load/` folder contains eight data files that share the key prefix `customer-fw.tbl`: `customer-fw.tbl0000`, `customer-fw.tbl0001`, and so on\. However, the same folder also contains the extraneous files `customer-fw.tbl.log` and `customer-fw.tbl-0001.bak`\. 

To ensure that you load all of the correct files, and only the correct files, use a manifest file\. The manifest is a text file in JSON format that explicitly lists the unique object key for each source file to be loaded\. The file objects can be in different folders or different buckets, but they must be in the same region\. For more information, see [MANIFEST](copy-parameters-data-source-s3.md#copy-manifest)\.

The following shows the `customer-fw-manifest` text\. 

```
{
  "entries": [
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-000"},
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-001"},
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-002"},
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-003"},
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-004"},    
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-005"},
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-006"}, 
    {"url":"s3://<your-bucket-name>/load/customer-fw.tbl-007"} 
    ]
}
```

**To load the data for the CUSTOMER table using the manifest file**

1. Open the file `customer-fw-manifest` in a text editor\.

1. Replace *<your\-bucket\-name>* with the name of your bucket\.

1. Save the file\.

1. Upload the file to the load folder on your bucket\.

1. Execute the following COPY command\.

   ```
   copy customer from 's3://<your-bucket-name>/load/customer-fw-manifest'
   credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
   fixedwidth 'c_custkey:10, c_name:25, c_address:25, c_city:10, c_nation:15, c_region :12, c_phone:15,c_mktsegment:10'
   maxerror 10 
   acceptinvchars as '^'
   manifest;
   ```

### Load the DWDATE table using DATEFORMAT<a name="tutorial-loading-load-dwdate"></a>

In this step, you use the DELIMITER and DATEFORMAT options to load the DWDATE table\.

When loading DATE and TIMESTAMP columns, COPY expects the default format, which is YYYY\-MM\-DD for dates and YYYY\-MM\-DD HH:MI:SS for timestamps\. If the load data does not use a default format, you can use DATEFORMAT and TIMEFORMAT to specify the format\. 

The following excerpt shows date formats in the DWDATE table\. Notice that the date formats in column two are inconsistent\.

```
19920104	1992-01-04          Sunday		January	1992	199201	Jan1992	1	4	4	1...
19920112	January 12, 1992	Monday		January	1992	199201	Jan1992	2	12	12	1...
19920120	January 20, 1992	Tuesday	    January	1992	199201	Jan1992	3	20	20	1...
```
<a name="tutorial-loading-dateformat"></a>
**DATEFORMAT**  
You can specify only one date format\. If the load data contains inconsistent formats, possibly in different columns, or if the format is not known at load time, you use DATEFORMAT with the `'auto'` argument\. When `'auto'` is specified, COPY recognizes any valid date or time format and convert it to the default format\. The `'auto'` option recognizes several formats that are not supported when using a DATEFORMAT and TIMEFORMAT string\. For more information, see [Using automatic recognition with DATEFORMAT and TIMEFORMAT](automatic-recognition.md)\. 

To load the DWDATE table, execute the following COPY command\.

```
copy dwdate from 's3://<your-bucket-name>/load/dwdate-tab.tbl'
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
delimiter '\t' 
dateformat 'auto';
```

### Load the LINEORDER table using multiple files<a name="tutorial-loading-load-lineorder"></a>

This step uses the GZIP and COMPUPDATE options to load the LINEORDER table\.

In this exercise, you load the LINEORDER table from a single data file and then load it again from multiple files\. Doing this enables you to compare the load times for the two methods\. 

**Note**  
The files for loading the LINEORDER table are provided in an AWS sample bucket\. You don't need to upload files for this step\.
<a name="tutorial-loading-gzip-lzop"></a>
**GZIP, LZOP and BZIP2**  
You can compress your files using either gzip, lzop, or bzip2 compression formats\. When loading from compressed files, COPY uncompresses the files during the load process\. Compressing your files saves storage space and shortens upload times\. 
<a name="tutorial-loading-compupdate"></a>
**COMPUPDATE**  
When COPY loads an empty table with no compression encodings, it analyzes the load data to determine the optimal encodings\. It then alters the table to use those encodings before beginning the load\. This analysis process takes time, but it occurs, at most, once per table\. To save time, you can skip this step by turning COMPUPDATE off\. To enable an accurate evaluation of COPY times, you turn COMPUPDATE off for this step\.
<a name="tutorial-loading-multiple-files"></a>
**Multiple Files**  
The COPY command can load data very efficiently when it loads from multiple files in parallel instead of from a single file\. You can split your data into files so that the number of files is a multiple of the number of slices in your cluster\. If you do, Amazon Redshift divides the workload and distributes the data evenly among the slices\. The number of slices per node depends on the node size of the cluster\. For more information about the number of slices that each node size has, go to [About clusters and nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\.

For example, the dc2\.large compute nodes used in this tutorial have two slices each, so the four\-node cluster has eight slices\. In previous steps, the load data was contained in eight files, even though the files are very small\. In this step, you compare the time difference between loading from a single large file and loading from multiple files\. 

The files you use for this tutorial contain about 15 million records and occupy about 1\.2 GB\. These files are very small in Amazon Redshift scale, but sufficient to demonstrate the performance advantage of loading from multiple files\. The files are large enough that the time required to download them and then upload them to Amazon S3 is excessive for this tutorial\. Thus, you load the files directly from an AWS sample bucket\. 

The following screenshot shows the data files for LINEORDER\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tutorial-load-lineorder-files.png)

**To evaluate the performance of COPY with multiple files**

1. Execute the following command to COPY from a single file\. Do not change the bucket name\.

   ```
   copy lineorder from 's3://awssampledb/load/lo/lineorder-single.tbl' 
   credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
   gzip
   compupdate off
   region 'us-east-1';
   ```

1. Your results should be similar to the following\. Note the execution time\.

   ```
   Warnings:
   Load into table 'lineorder' completed, 14996734 record(s) loaded successfully.
   
   0 row(s) affected.
   copy executed successfully
   
   Execution time: 51.56s
   ```

1. Execute the following command to COPY from multiple files\. Do not change the bucket name\.

   ```
   copy lineorder from 's3://awssampledb/load/lo/lineorder-multi.tbl' 
   credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
   gzip
   compupdate off
   region 'us-east-1';
   ```

1. Your results should be similar to the following\. Note the execution time\.

   ```
   Warnings:
   Load into table 'lineorder' completed, 14996734 record(s) loaded successfully.
   
   0 row(s) affected.
   copy executed successfully
   
   Execution time: 17.7s
   ```

1. Compare execution times\.

   In our example, the time to load 15 million records decreased from 51\.56 seconds to 17\.7 seconds, a reduction of 65\.7 percent\. 

   These results are based on using a four\-node cluster\. If your cluster has more nodes, the time savings is multiplied\. For typical Amazon Redshift clusters, with tens to hundreds of nodes, the difference is even more dramatic\. If you have a single node cluster, there is little difference between the execution times\. 

### Next step<a name="tutorial-loading-next-step6"></a>

[Step 6: Vacuum and analyze the database](tutorial-loading-data-vacuum.md)