# SYS\_LOAD\_ERROR\_DETAIL<a name="SYS_LOAD_ERROR_DETAIL"></a>

Use SYS\_LOAD\_ERROR\_DETAIL to view details of COPY command errors\. Each row represents a COPY command\. It contains both running and finished COPY commands\.

SYS\_LOAD\_ERROR\_DETAIL is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_LOAD_ERROR_DETAIL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_LOAD_ERROR_DETAIL.html)

## Sample queries<a name="SYS_LOAD_ERROR_DETAIL-sample-queries"></a>

The following query shows the load error details of copy command for specific query\.

```
SELECT query_id,
       table_id,
       start_time,
       trim(file_name) AS file_name, 
       trim(column_name) AS column_name, 
       trim(column_type) AS column_type, 
       trim(error_message) AS error_message 
FROM sys_load_error_detail 
WHERE query_id = 762949 
ORDER BY start_time 
LIMIT 10;
```

Sample output\.

```
 query_id | table_id |         start_time         |               file_name                  | column_name | column_type |                 error_message
----------+----------+----------------------------+------------------------------------------+-------------+-------------+------------------------------------------------
   762949 |   137885 | 2022-02-15 22:14:46.759151 | s3://load-test/copyfail/wrong_format_000 | id          | int4        | Invalid digit, Value 'a', Pos 0, Type: Integer
   762949 |   137885 | 2022-02-15 22:14:46.759151 | s3://load-test/copyfail/wrong_format_001 | id          | int4        | Invalid digit, Value 'a', Pos 0, Type: Integer
```