# INSERT \(external table\)<a name="r_INSERT_external_table"></a>

Inserts the results of a SELECT query into existing external tables on external catalog such as for AWS Glue, AWS Lake Formation, or an Apache Hive metastore\. Use the same AWS Identity and Access Management \(IAM\) role used for the CREATE EXTERNAL SCHEMA command to interact with external catalogs and Amazon S3\.

For nonpartitioned tables, the INSERT \(external table\) command writes data to the Amazon S3 location defined in the table, based on the specified table properties and file format\.

For partitioned tables, INSERT \(external table\) writes data to the Amazon S3 location according to the partition key specified in the table\. It also automatically registers new partitions in the external catalog after the INSERT operation completes\.

You can't run INSERT \(external table\) within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_INSERT_external_table-synopsis"></a>

```
INSERT INTO external_schema.table_name
{ select_statement }
```

## Parameters<a name="r_INSERT_external_table-parameters"></a>

 *external\_schema\.table\_name*   
The name of an existing external schema and a target external table to insert into\.

 *select\_statement*   
A statement that inserts one or more rows into the external table by defining any query\. All of the rows that the query produces are written to Amazon S3 in either text or Parquet format based on the table definition\. The query must return a column list that is compatible with the column data types in the external table\. However, the column names don't have to match\.

## Usage notes<a name="r_INSERT_external_table_usage_notes"></a>

The number of columns in the SELECT query must be the same as the sum of data columns and partition columns\. The location and the data type of each data column must match that of the external table\. The location of partition columns must be at the end of the SELECT query, in the same order they were defined in CREATE EXTERNAL TABLE command\. The column names don't have to match\.

In some cases, you might want to run the INSERT \(external table\) command on an AWS Glue Data Catalog or a Hive metastore\. In the case of AWS Glue, the IAM role used to create the external schema must have both read and write permissions on Amazon S3 and AWS Glue\. If you use an AWS Lake Formation catalog, This IAM role becomes the owner of the new Lake Formation table\. This IAM role must at least have the following permissions: 
+ SELECT, INSERT, UPDATE permission on the external table
+ Data location permission on the Amazon S3 path of the external table

To ensure that file names are unique, Amazon Redshift uses the following format for the name of each file uploaded to Amazon S3 by default\. 

`<date>_<time>_<microseconds>_<query_id>_<slice-number>_part_<part-number>.<format>`\.

An example is `20200303_004509_810669_1007_0001_part_00.parquet`\.

Consider the following when running the INSERT \(external table\) command:
+ External tables that have a format other than PARQUET or TEXTFILE aren't supported\.
+ This command supports existing table properties such as 'write\.parallel', 'write\.maxfilesize\.mb', 'compression\_type’, and 'serialization\.null\.format'\. To update those values, run the ALTER TABLE SET TABLE PROPERTIES command\.
+ The 'numRows’ table property is automatically updated toward the end of the INSERT operation\. The table property must be defined or added to the table already if it wasn't created by CREATE EXTERNAL TABLE AS operation\.
+ The LIMIT clause isn't supported in the outer SELECT query\. Instead, use a nested LIMIT clause\.
+ You can use the [STL\_UNLOAD\_LOG](r_STL_UNLOAD_LOG.md) table to track the files that got written to Amazon S3 by each INSERT \(external table\) operation\.
+ Amazon Redshift supports only Amazon S3 standard encryption for INSERT \(external table\)\.

## INSERT \(external table\) examples<a name="c_Examples_of_INSERT_external_table"></a>

The following example inserts the results of the SELECT statement into the external table\.

```
INSERT INTO spectrum.lineitem
SELECT * FROM local_lineitem;
```

The following example inserts the results of the SELECT statement into a partitioned external table using static partitioning\. The partition columns are hard\-coded in the SELECT statement\. The partition columns must be at the end of the query\.

```
INSERT INTO spectrum.customer
SELECT name, age, gender, 'May', 28 FROM local_customer;
```

The following example inserts the results of the SELECT statement into a partitioned external table using dynamic partitioning\. The partition columns aren't hard\-coded\. Data is automatically added to the existing partition folders, or to new folders if a new partition is added\.

```
INSERT INTO spectrum.customer
SELECT name, age, gender, month, day FROM local_customer;
```