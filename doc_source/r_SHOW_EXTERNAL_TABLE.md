# SHOW EXTERNAL TABLE<a name="r_SHOW_EXTERNAL_TABLE"></a>

Shows the definition of an external table, including table attributes and column attributes\. You can use the output of the SHOW EXTERNAL TABLE statement to recreate the table\. 

For more information about external table creation, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\. 

## Syntax<a name="r_SHOW_EXTERNAL_TABLE-synopsis"></a>

```
SHOW EXTERNAL TABLE external_schema.table_name [ PARTITION ]
```

## Parameters<a name="r_SHOW_EXTERNAL_TABLE-parameters"></a>

 *external\_schema*   
The name of the associated external schema\. 

 *table\_name*   
The name of the table to show\. 

PARTITION   
Displays ALTER TABLE statements to add partitions to the table definition\. 

## Examples<a name="r_SHOW_EXTERNAL_TABLE-examples"></a>

The following examples are based on an external table defined as follows:

```
CREATE EXTERNAL TABLE my_schema.alldatatypes_parquet_test_partitioned (
     csmallint smallint,
     cint int,
     cbigint bigint,
     cfloat float4,
     cdouble float8,
     cchar char(10),
     cvarchar varchar(255),
     cdecimal_small decimal(18,9),
     cdecimal_big decimal(30,15),
     ctimestamp TIMESTAMP,
     cboolean boolean,
     cstring varchar(16383)
)
PARTITIONED BY (cdate date, ctime TIMESTAMP)
STORED AS PARQUET
LOCATION 's3://mybucket-test-copy/alldatatypes_parquet_partitioned';
```

Following is an example of the SHOW EXTERNAL TABLE command and output for the table `my_schema.alldatatypes_parquet_test_partitioned`\.

```
SHOW EXTERNAL TABLE my_schema.alldatatypes_parquet_test_partitioned;
```

```
"CREATE EXTERNAL TABLE my_schema.alldatatypes_parquet_test_partitioned (
    csmallint smallint,
    cint int,
    cbigint bigint,
    cfloat float4,
    cdouble float8,
    cchar char(10),
    cvarchar varchar(255),
    cdecimal_small decimal(18,9),
    cdecimal_big decimal(30,15),
    ctimestamp timestamp,
    cboolean boolean,
    cstring varchar(16383)
)
PARTITIONED BY (cdate date, ctime timestamp)
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION 's3://mybucket-test-copy/alldatatypes_parquet_partitioned';"
```

Following is an example of the SHOW EXTERNAL TABLE command and output when using the `PARTITION` parameter\. The output contains ALTER TABLE statements to add partitions to the table definition\.

```
SHOW EXTERNAL TABLE my_schema.alldatatypes_parquet_test_partitioned PARTITION;
```

```
"CREATE EXTERNAL TABLE my_schema.alldatatypes_parquet_test_partitioned (
    csmallint smallint,
    cint int,
    cbigint bigint,
    cfloat float4,
    cdouble float8,
    cchar char(10),
    cvarchar varchar(255),
    cdecimal_small decimal(18,9),
    cdecimal_big decimal(30,15),
    ctimestamp timestamp,
    cboolean boolean,
    cstring varchar(16383)
)
PARTITIONED BY (cdate date)
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION 's3://mybucket-test-copy/alldatatypes_parquet_partitioned';
ALTER TABLE my_schema.alldatatypes_parquet_test_partitioned ADD IF NOT EXISTS PARTITION (cdate='2021-01-01') LOCATION 's3://mybucket-test-copy/alldatatypes_parquet_partitioned2/cdate=2021-01-01';
ALTER TABLE my_schema.alldatatypes_parquet_test_partitioned ADD IF NOT EXISTS PARTITION (cdate='2021-01-02') LOCATION 's3://mybucket-test-copy/alldatatypes_parquet_partitioned2/cdate=2021-01-02';"
```