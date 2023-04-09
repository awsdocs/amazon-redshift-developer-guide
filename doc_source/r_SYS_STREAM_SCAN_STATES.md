# SYS\_STREAM\_SCAN\_STATES<a name="r_SYS_STREAM_SCAN_STATES"></a>

Records scan states for records loaded via streaming ingestion\.

SYS\_STREAM\_SCAN\_STATES is visible only to the superuser\.

## Table columns<a name="r_SYS_STREAM_SCAN_STATES-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SYS_STREAM_SCAN_STATES.html)

The following query shows stream and topic data for specific queries\.

```
select query_id,mv_name::varchar,external_schema_name::varchar,stream_name::varchar,sum(scanned_rows) total_records,
sum(scanned_bytes) total_bytes from sys_stream_scan_states where query in (5401180,8601939) group by 1,2,3,4;

  query_id  |    mv_name     | external_schema_name |   stream_name   | total_records |  total_bytes
------------+----------------+----------------------+-----------------+---------------+----------------
 5401180    | kinesistest    | kinesis              | kinesisstream   |    1493255696 | 3209006490704
 8601939    | msktest        | msk                  | mskstream       |      14677023 |   31056580668
(2 rows)
```