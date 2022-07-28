# Usage notes<a name="r_CREATE_EXTERNAL_TABLE_usage"></a>

You can't view details for Amazon Redshift Spectrum tables using the same resources that you use for standard Amazon Redshift tables, such as [PG\_TABLE\_DEF](r_PG_TABLE_DEF.md), [STV\_TBL\_PERM](r_STV_TBL_PERM.md), PG\_CLASS, or information\_schema\. If your business intelligence or analytics tool doesn't recognize Redshift Spectrum external tables, configure your application to query [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md)\.

## CREATE EXTERNAL TABLE AS<a name="r_CETAS"></a>

In some cases, you might run the CREATE EXTERNAL TABLE AS command on an AWS Glue Data Catalog, AWS Lake Formation external catalog, or Apache Hive metastore\. In such cases, you use an AWS Identity and Access Management \(IAM\) role to create the external schema\. This IAM role must have both read and write permissions on Amazon S3\. 

If you use a Lake Formation catalog, the IAM role must have the permission to create table in the catalog\. In this case, it must also have the data lake location permission on the target Amazon S3 path\. This IAM role becomes the owner of the new AWS Lake Formation table\.

To ensure that file names are unique, Amazon Redshift uses the following format for the name of each file uploaded to Amazon S3 by default\.

`<date>_<time>_<microseconds>_<query_id>_<slice-number>_part_<part-number>.<format>`\.

 An example is `20200303_004509_810669_1007_0001_part_00.parquet`\.

Consider the following when running the CREATE EXTERNAL TABLE AS command:
+ The Amazon S3 location must be empty\.
+ Amazon Redshift only supports PARQUET and TEXTFILE formats when using the STORED AS clause\.
+ You don't need to define a column definition list\. Column names and column data types of the new external table are derived directly from the SELECT query\.
+ You don't need to define the data type of the partition column in the PARTITIONED BY clause\. If you specify a partition key, the name of this column must exist in the SELECT query result\. When having multiple partition columns, their order in the SELECT query doesn't matter\. Amazon Redshift uses their order defined in the PARTITIONED BY clause to create the external table\.
+ Amazon Redshift automatically partitions output files into partition folders based on the partition key values\. By default, Amazon Redshift removes partition columns from the output files\.
+ The LINES TERMINATED BY 'delimiter' clause isn't supported\.
+ The ROW FORMAT SERDE 'serde\_name' clause isn't supported\.
+ The use of manifest files isn't supported\. Thus, you can't define the LOCATION clause to a manifest file on Amazon S3\.
+ Amazon Redshift automatically updates the 'numRows' table property at the end of the command\.
+ The 'compression\_type' table property only accepts 'none' or 'snappy' for the PARQUET file format\.
+ Amazon Redshift doesn't allow the LIMIT clause in the outer SELECT query\. Instead, you can use a nested LIMIT clause\.
+ You can use STL\_UNLOAD\_LOG to track the files that are written to Amazon S3 by each CREATE EXTERNAL TABLE AS operation\.

## Permissions to create and query external tables<a name="r_CREATE_EXTERNAL_TABLE_usage-permissions"></a>

To create external tables, make sure that you're the owner of the external schema or a superuser\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md)\. The following example changes the owner of the `spectrum_schema` schema to `newowner`\.

```
alter schema spectrum_schema owner to newowner;
```

To run a Redshift Spectrum query, you need the following permissions:
+ Usage permission on the schema 
+ Permission to create temporary tables in the current database 

The following example grants usage permission on the schema `spectrum_schema` to the `spectrumusers` user group\.

```
grant usage on schema spectrum_schema to group spectrumusers;
```

The following example grants temporary permission on the database `spectrumdb` to the `spectrumusers` user group\. 

```
grant temp on database spectrumdb to group spectrumusers;
```

## Pseudocolumns<a name="r_CREATE_EXTERNAL_TABLE_usage-pseudocolumns"></a>

By default, Amazon Redshift creates external tables with the pseudocolumns *$path* and *$size*\. Select these columns to view the path to the data files on Amazon S3 and the size of the data files for each row returned by a query\. The *$path* and *$size* column names must be delimited with double quotation marks\. A *SELECT \** clause doesn't return the pseudocolumns \. You must explicitly include the *$path* and *$size* column names in your query, as the following example shows\.

```
select "$path", "$size"
from spectrum.sales_part
where saledate = '2008-12-01';
```

You can disable creation of pseudocolumns for a session by setting the *spectrum\_enable\_pseudo\_columns* configuration parameter to *false*\. 

**Important**  
Selecting *$size* or *$path* incurs charges because Redshift Spectrum scans the data files in Amazon S3 to determine the size of the result set\. For more information, see [Amazon Redshift Pricing](https://aws.amazon.com/redshift/pricing/)\.

## Setting data handling options<a name="r_CREATE_EXTERNAL_TABLE_usage-data-handling"></a>

You can set table parameters to specify input handling for data being queried in external tables, including: 
+ Surplus characters in columns containing VARCHAR, CHAR, and string data\. For more information, see the external table property `surplus_char_handling`\.
+ Invalid characters in columns containing VARCHAR, CHAR, and string data\. For more information, see the external table property `invalid_char_handling`\.
+ Replacement character to use when you specify REPLACE for the external table property `invalid_char_handling`\.
+ Cast overflow handling in columns containing integer and decimal data\. For more information, see the external table property `numeric_overflow_handling`\.