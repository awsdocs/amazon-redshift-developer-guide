# SYS\_UNLOAD\_HISTORY<a name="SYS_UNLOAD_HISTORY"></a>

Use SYS\_UNLOAD\_HISTORY to view details of UNLOAD commands\. Each row represents a UNLOAD command with accumulated statistics for some of the fields\. It contains both running and finished UNLOAD commands\.

SYS\_UNLOAD\_HISTORY is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_UNLOAD_HISTORY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_UNLOAD_HISTORY.html)

## Sample queries<a name="SYS_UNLOAD_HISTORY-sample-queries"></a>

The following query shows the unloaded query details, including format, rows and file count of unload command\.

```
SELECT query_id,
       file_format,
       start_time,
       duration,
       unloaded_rows,
       unloaded_files_count
FROM sys_unload_history
ORDER BY query_id,
file_format limit 100;
```

Sample output\.

```
 query_id | file_format |         start_time         | duration | unloaded_rows | unloaded_files_count
----------+-------------+----------------------------+----------+---------------+----------------------
   527067 | Text        | 2022-02-09 05:18:35.844452 |  5932478 |            10 |                    1
```