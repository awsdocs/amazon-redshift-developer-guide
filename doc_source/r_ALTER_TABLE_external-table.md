# ALTER EXTERNAL TABLE examples<a name="r_ALTER_TABLE_external-table"></a>

The following example sets the numRows table property for the SPECTRUM\.SALES external table to 170,000 rows\.

```
alter table spectrum.sales 
set table properties ('numRows'='170000');
```

The following example changes the location for the SPECTRUM\.SALES external table\.

```
alter table spectrum.sales 
set location 's3://awssampledbuswest2/tickit/spectrum/sales/';
```

The following example changes the format for the SPECTRUM\.SALES external table to Parquet\.

```
alter table spectrum.sales 
set file format parquet;
```

The following example adds one partition for the table SPECTRUM\.SALES\_PART\.

```
alter table spectrum.sales_part
add if not exists partition(saledate='2008-01-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
```

The following example adds three partitions for the table SPECTRUM\.SALES\_PART\.

```
alter table spectrum.sales_part add if not exists
partition(saledate='2008-01-01')
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/'
partition(saledate='2008-02-01')
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/'
partition(saledate='2008-03-01')
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03/';
```

The following example alters SPECTRUM\.SALES\_PART to drop the partition with `saledate='2008-01-01''`\.

```
alter table spectrum.sales_part
drop partition(saledate='2008-01-01');
```

The following example sets a new Amazon S3 path for the partition with `saledate='2008-01-01'`\.

```
alter table spectrum.sales_part
partition(saledate='2008-01-01') 
set location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01-01/';
```

The following example changes the name of `sales_date` to `transaction_date`\. 

```
alter table spectrum.sales rename column sales_date to transaction_date;
```

The following example sets the column mapping to position mapping for an external table that uses optimized row columnar \(ORC\) format\.

```
alter table spectrum.orc_example 
set table properties('orc.schema.resolution'='position');
```

The following example sets the column mapping to name mapping for an external table that uses ORC format\.

```
alter table spectrum.orc_example 
set table properties('orc.schema.resolution'='name');
```