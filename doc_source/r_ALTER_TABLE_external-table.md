# Alter External Table Examples<a name="r_ALTER_TABLE_external-table"></a>

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
<<<<<<< HEAD
add if not exists partition(saledate='2008-01-01') 
=======
add partition if not exists (saledate='2008-01-01') 
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
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