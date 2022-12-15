# SYS\_EXTERNAL\_QUERY\_DETAIL<a name="SYS_EXTERNAL_QUERY_DETAIL"></a>

Use SYS\_EXTERNAL\_QUERY\_DETAIL to view details for queries at a segment level\. Each row represents a segment from a particular WLM query with details like the number of rows processed, number of bytes processed, and partition info of external tables in Amazon S3\. Each row in this view will also have a corresponding entry in the SYS\_QUERY\_DETAIL view, except this view has more detail information related to external query processing\. 

SYS\_EXTERNAL\_QUERY\_DETAIL is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_EXTERNAL_QUERY_DETAIL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_EXTERNAL_QUERY_DETAIL.html)

## Sample queries<a name="SYS_EXTERNAL_QUERY_DETAIL-sample-queries"></a>

The following query shows the external query details\.

```
SELECT query_id,
       segment_id,
       start_time,
       end_time,
       total_partitions,
       qualified_partitions,
       scanned_files,
       returned_rows,
       returned_bytes,
       trim(external_query_text) query_text,
       trim(file_location) file_location
FROM sys_external_query_detail
ORDER BY query_id, start_time DESC
LIMIT 2;
```

Sample output\.

```
 query_id | segment_id |         start_time         |          end_time          | total_partitions | qualified_partitions | scanned_files | returned_rows | returned_bytes | query_text | file_location
----------+------------+----------------------------+----------------------------+------------------+----------------------+---------------+---------------+----------------+------------+---------------
   763251 |          0 | 2022-02-15 22:32:23.312448 | 2022-02-15 22:32:24.036023 |                3 |                    3 |             3 |         38203 |        2683414 |            |
   763254 |          0 | 2022-02-15 22:32:40.17103  | 2022-02-15 22:32:40.839313 |                3 |                    3 |             3 |         38203 |        2683414 |            |
```