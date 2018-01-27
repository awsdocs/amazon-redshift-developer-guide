# Creating External Tables for Amazon Redshift Spectrum<a name="c-spectrum-external-tables"></a>

Amazon Redshift Spectrum uses external tables to query data that is stored in Amazon S3\. You can query an external table using the same SELECT syntax you use with other Amazon Redshift tables\. External tables are read\-only\. You can't write to an external table\.

You create an external table in an external schema\. To create external tables, you must be the owner of the external schema or a superuser\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. The following example changes the owner of the `spectrum_schema` schema to `newowner`\.

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

You can create an external table in Amazon Redshift, AWS Glue, Amazon Athena, or an Apache Hive metastore\. For more information, see [Getting Started Using AWS Glue](http://docs.aws.amazon.com/glue/latest/dg/getting-started.html) in the *AWS Glue Developer Guide*, [Getting Started](http://docs.aws.amazon.com/athena/latest/ug/getting-started.html) in the *Amazon Athena User Guide*, or [Apache Hive](http://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive.html) in the *Amazon EMR Developer Guide*\. 

**Important**  
Redshift Spectrum doesn't support nested data types, such as STRUCT, ARRAY, and MAP\.

If your external table is defined in AWS Glue, Athena, or a Hive metastore, you first create an external schema that references the external database\. Then you can reference the external table in your SELECT statement by prefixing the table name with the schema name, without needing to create the table in Amazon Redshift\. For more information, see [Creating External Schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)\. 

For example, suppose that you have an external table named `lineitem_athena` defined in an Athena external catalog\. In this case, you can define an external schema named `athena_schema`, then query the table using the following SELECT statement\.

```
select count(*) from athena_schema.lineitem_athena;
```

To define an external table in Amazon Redshift, use the [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md) command\. The external table statement defines the table columns, the format of your data files, and the location of your data in Amazon S3\. Redshift Spectrum scans the files in the specified folder and any subfolders\. Redshift Spectrum ignores hidden files and files that begin with a period, underscore, or hash mark \( \. , \_, or \#\) or end with a tilde \(\~\)\. 

The following example creates a table named SALES in the Amazon Redshift external schema named `spectrum`\. The data is in tab\-delimited text files\.

```
create external table spectrum.sales(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
dateid smallint,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
row format delimited
fields terminated by '\t'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales/'
table properties ('numRows'='172000');
```

To view external tables, query the [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) system view\. 

## Pseudocolumns<a name="c-spectrum-external-tables-pseudocolumns"></a>

By default, Amazon Redshift creates external tables with the pseudocolumns `$path` and `$size`\. Select these columns to view the path to the data files on Amazon S3 and the size of the data files for each row returned by a query\. The `$path` and `$size` column names must be delimited with double quotation marks\. A `SELECT *` clause doesn't return the pseudocolumns\. You must explicitly include the $path and $size column names in your query, as the following example shows\.

```
select "$path", "$size"
from spectrum.sales_part
where saledate = '2008-12-01';
```

You can disable creation of pseudocolumns for a session by setting the `spectrum_enable_pseudo_columns` configuration parameter to false\. 

**Important**  
Selecting $size or $path incurs charges because Redshift Spectrum scans the data files on Amazon S3 to determine the size of the result set\. For more information, see [Amazon Redshift Pricing](https://aws.amazon.com/redshift/pricing/)\.

### Pseudocolumns Example<a name="c-spectrum-external-tables-pseudocolumns-example"></a>

The following example returns the total size of related data files for an external table\.

```
select distinct "$path", "$size"
from spectrum.sales_part;

 $path                                 | $size
---------------------------------------+-------
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/ |  1616
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
```

## Partitioning Redshift Spectrum External Tables<a name="c-spectrum-external-tables-partitioning"></a>

When you partition your data, you can restrict the amount of data Redshift Spectrum scans by filtering on the partition key\. You can partition your data by any key\. A common practice is to partition the data based on time\. For example, you might choose to partition by year, month, date, and hour\. If you have data coming from multiple sources, you might partition by a data source identifier and date\. 

The following procedure describes how to partition your data\.

**To partition your data**

1. Store your data in folders in Amazon S3 according to your partition key\. 

   Create one folder for each partition value and name the folder with the partition key and value\. For example, if you partition by date, you might have folders named `saledate=2017-04-31`, `saledate=2017-04-30`, and so on\. Redshift Spectrum scans the files in the partition folder and any subfolders\. Redshift Spectrum ignores hidden files and files that begin with a period, underscore, or hash mark \( \. , \_, or \#\) or end with a tilde \(\~\)\. 

1. Create an external table and specify the partition key in the PARTITIONED BY clause\. 

   The partition key can't be the name of a table column\. The data type can be any standard Amazon Redshift data type except TIMESTAMPTZ\. You can use the DATE type only for a partition column, not for other external table columns\. 

1. Add the partitions\. 

   Using [ALTER TABLE](r_ALTER_TABLE.md) … ADD PARTITION, add each partition, specifying the partition column and key value, and the location of the partition folder in Amazon S3\. You can add only one partition in each ALTER TABLE statement\. The following example adds partitions for '2008\-01\-01' and '2008\-02\-01'\.

   ```
   alter table spectrum.sales_part
   add partition(saledate='2008-01-01') 
   location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
   
   alter table spectrum.sales_part
   add partition(saledate='2008-02-01') 
   location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/';
   ```

### Partitioning Data Example<a name="c-spectrum-external-tables-partitioning-example"></a>

In this example, you'll create an external table that is partitioned by month\.

The sample data for this example is located in an Amazon S3 buckets that gives read access to all authenticated AWS users\. Your cluster and your external data files must be in the same region\. The sample data bucket is in the US West \(Oregon\) Region \(us\-west\-2\)\. To access the data using Redshift Spectrum, your cluster must also be in us\-west\-2\. To list the folders in Amazon S3, run the following command\.

```
aws s3 ls s3://awssampledbuswest2/tickit/spectrum/sales_partition/
```

If you don't already have an external schema, run the following command, substituting the Amazon Resource Name \(ARN\) for your AWS Identity and Access Management \(IAM\) role\.

```
create external schema spectrum
from data catalog
database 'spectrumdb'
iam_role 'arn:aws:iam::123456789012:role/myspectrumrole'
create external database if not exists;
```

To create an external table partitioned by date, run the following command\.

```
create external table spectrum.sales_part(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
dateid smallint,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
partitioned by (saledate date)
row format delimited
fields terminated by '|'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/'
table properties ('numRows'='172000');
```

To add the partitions, run the following ALTER TABLE commands\.

```
alter table spectrum.sales_part
add partition(saledate='2008-01-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
alter table spectrum.sales_part
add partition(saledate='2008-02-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/';
alter table spectrum.sales_part
add partition(saledate='2008-03-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03/';
alter table spectrum.sales_part
add partition(saledate='2008-04-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04/';
alter table spectrum.sales_part
add partition(saledate='2008-05-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05/';
alter table spectrum.sales_part
add partition(saledate='2008-06-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06/';
alter table spectrum.sales_part
add partition(saledate='2008-07-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07/';
alter table spectrum.sales_part
add partition(saledate='2008-08-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08/';
alter table spectrum.sales_part
add partition(saledate='2008-09-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09/';
alter table spectrum.sales_part
add partition(saledate='2008-10-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10/';
alter table spectrum.sales_part
add partition(saledate='2008-11-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11/';
alter table spectrum.sales_part
add partition(saledate='2008-12-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12/';
```

Run the following query to select data from the partitioned table\.

```
select top 10 spectrum.sales_part.eventid, sum(spectrum.sales_part.pricepaid) 
from spectrum.sales_part, event
where spectrum.sales_part.eventid = event.eventid
  and spectrum.sales_part.pricepaid > 30
  and saledate = '2008-12-01'
group by spectrum.sales_part.eventid
order by 2 desc;
```

To view external table partitions, query the [SVV\_EXTERNAL\_PARTITIONS](r_SVV_EXTERNAL_PARTITIONS.md) system view\.

```
select schemaname, tablename, values, location from svv_external_partitions
where tablename = 'sales_part';
```

```
schemaname | tablename  | values         | location                                         
-----------+------------+----------------+--------------------------------------------------
spectrum   | sales_part | ["2008-01-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01
spectrum   | sales_part | ["2008-02-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02
spectrum   | sales_part | ["2008-03-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03
spectrum   | sales_part | ["2008-04-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04
spectrum   | sales_part | ["2008-05-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05
spectrum   | sales_part | ["2008-06-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06
spectrum   | sales_part | ["2008-07-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07
spectrum   | sales_part | ["2008-08-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08
spectrum   | sales_part | ["2008-09-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09
spectrum   | sales_part | ["2008-10-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10
spectrum   | sales_part | ["2008-11-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11
spectrum   | sales_part | ["2008-12-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12
```