# COPY<a name="r_COPY"></a>

Loads data into a table from data files or from an Amazon DynamoDB table\. The files can be located in an Amazon Simple Storage Service \(Amazon S3\) bucket, an Amazon EMR cluster, or a remote host that is accessed using a Secure Shell \(SSH\) connection\.

**Note**  
Amazon Redshift Spectrum external tables are read\-only\. You can't COPY to an external table\.

The COPY command appends the new input data to any existing rows in the table\.

The maximum size of a single input row from any source is 4 MB\.

**Note**  
To use the COPY command, you must have [INSERT](r_GRANT.md#grant-insert) privilege for the Amazon Redshift table\. 

**Topics**
+ [COPY syntax](#r_COPY-syntax)
+ [COPY syntax overview](#r_COPY-syntax-overview)
+ [COPY parameter reference](r_COPY-parameters.md)
+ [Usage notes](r_COPY_usage_notes.md)
+ [COPY examples](r_COPY_command_examples.md)

## COPY syntax<a name="r_COPY-syntax"></a>

```
COPY table-name 
[ column-list ]
FROM data_source
authorization
[ [ FORMAT ] [ AS ] data_format ] 
[ parameter [ argument ] [, ... ] ]
```

## COPY syntax overview<a name="r_COPY-syntax-overview"></a>

You can perform a COPY operation with as few as three parameters: a table name, a data source, and authorization to access the data\. 

Amazon Redshift extends the functionality of the COPY command to enable you to load data in several data formats from multiple data sources, control access to load data, manage data transformations, and manage the load operation\. 

This section presents the required COPY command parameters and groups the optional parameters by function\. Subsequent topics describe each parameter and explain how various options work together\. You can also go directly to a parameter description by using the alphabetical parameter list\. 

**Topics**
+ [Required parameters](#r_COPY-syntax-required-parameters)
+ [Optional parameters](#r_COPY-syntax-overview-optional-parameters)
+ [Using the COPY command](#r_COPY-using-the-copy-command)

### Required parameters<a name="r_COPY-syntax-required-parameters"></a>

The COPY command requires three elements: 
+ [Table Name](#r_COPY-syntax-overview-table-name)
+ [Data Source](#r_COPY-syntax-overview-data-source)
+ [Authorization](#r_COPY-syntax-overview-credentials)

The simplest COPY command uses the following format\. 

```
COPY table-name 
FROM data-source
authorization;
```

The following example creates a table named CATDEMO, and then loads the table with sample data from a data file in Amazon S3 named `category_pipe.txt`\. 

```
create table catdemo(catid smallint, catgroup varchar(10), catname varchar(10), catdesc varchar(50));
```

In the following example, the data source for the COPY command is a data file named `category_pipe.txt` in the `tickit` folder of an Amazon S3 bucket named `awssampledbuswest2`\. The COPY command is authorized to access the Amazon S3 bucket through an AWS Identity and Access Management \(IAM\) role\. If your cluster has an existing IAM role with permission to access Amazon S3 attached, you can substitute your role's Amazon Resource Name \(ARN\) in the following COPY command and run it\.

```
copy catdemo
from 's3://awssampledbuswest2/tickit/category_pipe.txt'
iam_role 'arn:aws:iam::<aws-account-id>:role/<role-name>'
region 'us-west-2';
```

For steps to create an IAM role, see [Step 2: Create an IAM Role](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-create-an-iam-role.html) in the Amazon Redshift Getting Started\. For complete instructions on how to use COPY commands to load sample data, including instructions for loading data from other AWS regions, see [Step 6: Load Sample Data from Amazon S3](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-create-sample-db.html) in the Amazon Redshift Getting Started\.\.

*table\-name*  <a name="r_COPY-syntax-overview-table-name"></a>
The name of the target table for the COPY command\. The table must already exist in the database\. The table can be temporary or persistent\. The COPY command appends the new input data to any existing rows in the table\.

FROM *data\-source*  <a name="r_COPY-syntax-overview-data-source"></a>
The location of the source data to be loaded into the target table\. A manifest file can be specified with some data sources\.   
The most commonly used data repository is an Amazon S3 bucket\. You can also load from data files located in an Amazon EMR cluster, an Amazon EC2 instance, or a remote host that your cluster can access using an SSH connection, or you can load directly from a DynamoDB table\.   
+ [COPY from Amazon S3](copy-parameters-data-source-s3.md)
+ [COPY from Amazon EMR](copy-parameters-data-source-emr.md) 
+ [COPY from remote host \(SSH\)](copy-parameters-data-source-ssh.md)
+ [COPY from Amazon DynamoDB](copy-parameters-data-source-dynamodb.md)

Authorization  <a name="r_COPY-syntax-overview-credentials"></a>
A clause that indicates the method that your cluster uses for authentication and authorization to access other AWS resources\. The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an IAM role that is attached to your cluster or by providing the access key ID and secret access key for an IAM user\.   
+ [Authorization parameters](copy-parameters-authorization.md) 
+ [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based) 
+ [Key\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based) 

### Optional parameters<a name="r_COPY-syntax-overview-optional-parameters"></a>

You can optionally specify how COPY maps field data to columns in the target table, define source data attributes to enable the COPY command to correctly read and parse the source data, and manage which operations the COPY command performs during the load process\. 
+ [Column mapping options](copy-parameters-column-mapping.md)
+ [Data format parameters](#r_COPY-syntax-overview-data-format)
+ [Data conversion parameters](#r_COPY-syntax-overview-data-conversion)
+ [Data load operations](#r_COPY-syntax-overview-data-load)

#### Column mapping<a name="r_COPY-syntax-overview-column-mapping"></a>

By default, COPY inserts field values into the target table's columns in the same order as the fields occur in the data files\. If the default column order will not work, you can specify a column list or use JSONPath expressions to map source data fields to the target columns\. 
+ [Column List](copy-parameters-column-mapping.md#copy-column-list)
+ [JSONPaths File](copy-parameters-column-mapping.md#copy-column-mapping-jsonpaths)

#### Data format parameters<a name="r_COPY-syntax-overview-data-format"></a>

You can load data from text files in fixed\-width, character\-delimited, comma\-separated values \(CSV\), or JSON format, or from Avro files\.

By default, the COPY command expects the source data to be in character\-delimited UTF\-8 text files\. The default delimiter is a pipe character \( \| \)\. If the source data is in another format, use the following parameters to specify the data format\.
+ [FORMAT](copy-parameters-data-format.md#copy-format)
+ [CSV](copy-parameters-data-format.md#copy-csv)
+ [DELIMITER](copy-parameters-data-format.md#copy-delimiter) 
+ [FIXEDWIDTH](copy-parameters-data-format.md#copy-fixedwidth) 
+ [SHAPEFILE](copy-parameters-data-format.md#copy-shapefile) 
+ [AVRO](copy-parameters-data-format.md#copy-avro) 
+ [JSON](copy-parameters-data-format.md#copy-json) 
+ [ENCRYPTED](copy-parameters-data-source-s3.md#copy-encrypted) 
+ [BZIP2](copy-parameters-file-compression.md#copy-bzip2) 
+ [GZIP](copy-parameters-file-compression.md#copy-gzip) 
+ [LZOP](copy-parameters-file-compression.md#copy-lzop) 
+ [PARQUET](copy-parameters-data-format.md#copy-parquet) 
+ [ORC](copy-parameters-data-format.md#copy-orc) 
+ [ZSTD](copy-parameters-file-compression.md#copy-zstd) 

#### Data conversion parameters<a name="r_COPY-syntax-overview-data-conversion"></a>

As it loads the table, COPY attempts to implicitly convert the strings in the source data to the data type of the target column\. If you need to specify a conversion that is different from the default behavior, or if the default conversion results in errors, you can manage data conversions by specifying the following parameters\.
+ [ACCEPTANYDATE](copy-parameters-data-conversion.md#copy-acceptanydate) 
+ [ACCEPTINVCHARS](copy-parameters-data-conversion.md#copy-acceptinvchars) 
+ [BLANKSASNULL](copy-parameters-data-conversion.md#copy-blanksasnull) 
+ [DATEFORMAT](copy-parameters-data-conversion.md#copy-dateformat) 
+ [EMPTYASNULL](copy-parameters-data-conversion.md#copy-emptyasnull) 
+ [ENCODING](copy-parameters-data-conversion.md#copy-encoding) 
+ [ESCAPE](copy-parameters-data-conversion.md#copy-escape) 
+ [EXPLICIT_IDS](copy-parameters-data-conversion.md#copy-explicit-ids) 
+ [FILLRECORD](copy-parameters-data-conversion.md#copy-fillrecord) 
+ [IGNOREBLANKLINES](copy-parameters-data-conversion.md#copy-ignoreblanklines) 
+ [IGNOREHEADER](copy-parameters-data-conversion.md#copy-ignoreheader) 
+ [NULL AS](copy-parameters-data-conversion.md#copy-null-as) 
+ [REMOVEQUOTES](copy-parameters-data-conversion.md#copy-removequotes) 
+ [ROUNDEC](copy-parameters-data-conversion.md#copy-roundec) 
+ [TIMEFORMAT](copy-parameters-data-conversion.md#copy-timeformat) 
+ [TRIMBLANKS](copy-parameters-data-conversion.md#copy-trimblanks) 
+ [TRUNCATECOLUMNS](copy-parameters-data-conversion.md#copy-truncatecolumns) 

#### Data load operations<a name="r_COPY-syntax-overview-data-load"></a>

Manage the default behavior of the load operation for troubleshooting or to reduce load times by specifying the following parameters\. 
+ [COMPROWS](copy-parameters-data-load.md#copy-comprows) 
+ [COMPUPDATE](copy-parameters-data-load.md#copy-compupdate) 
+ [MAXERROR](copy-parameters-data-load.md#copy-maxerror) 
+ [NOLOAD](copy-parameters-data-load.md#copy-noload) 
+ [STATUPDATE](copy-parameters-data-load.md#copy-statupdate) 

### Using the COPY command<a name="r_COPY-using-the-copy-command"></a>

For more information about how to use the COPY command, see the following topics: 
+ [COPY examples](r_COPY_command_examples.md)
+ [Usage notes](r_COPY_usage_notes.md)
+ [Tutorial: Loading data from Amazon S3](tutorial-loading-data.md)
+ [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md)
+ [Using a COPY command to load data](t_Loading_tables_with_the_COPY_command.md)
  + [Loading data from Amazon S3](t_Loading-data-from-S3.md)
  + [Loading data from Amazon EMR](loading-data-from-emr.md)
  + [Loading data from remote hosts](loading-data-from-remote-hosts.md) 
  + [Loading data from an Amazon DynamoDB table](t_Loading-data-from-dynamodb.md)
+ [Troubleshooting data loads](t_Troubleshooting_load_errors.md)