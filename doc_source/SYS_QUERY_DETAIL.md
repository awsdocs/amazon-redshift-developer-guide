# SYS\_QUERY\_DETAIL<a name="SYS_QUERY_DETAIL"></a>

Use SYS\_QUERY\_DETAIL to view details for queries at a step level\. Each row represents a step from a particular WLM query with details\. This view contains many types of queries such as DDL, DML, and utility commands \(for example, copy and unload\)\. Some columns might not be relevant depending on the query type\. For example, external\_scanned\_bytes is not relevant to internal tables\.

**Note**  
A query that does not have a corresponding rewritten query has an entry only in the SYS\_QUERY\_HISTORY view, not in the SYS\_QUERY\_DETAIL view\.

SYS\_QUERY\_DETAIL is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_QUERY_DETAIL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_QUERY_DETAIL.html)

## Sample queries<a name="SYS_QUERY_DETAIL-sample-queries"></a>

The following example returns the output of SYS\_QUERY\_DETAIL\.

The following query shows the query metadata detail at step level, including step name, input\_bytes, output\_bytes, input\_rows, output\_rows\.

```
SELECT query_id,
       child_query_sequence,
       stream_id,
       segment_id,
       step_id,
       trim(step_name) AS step_name,
       duration,
       input_bytes,
       output_bytes,
       input_rows,
       output_rows
FROM sys_query_detail
WHERE query_id IN (193929)
ORDER BY query_id,
         stream_id,
         segment_id,
         step_id DESC;
```

Sample output\.

```
 query_id | child_query_sequence | stream_id | segment_id | step_id | step_name  |    duration     | input_bytes | output_bytes | input_rows | output_rows
----------+----------------------+-----------+------------+---------+------------+-----------------+-------------+--------------+------------+-------------
   193929 |                    2 |         0 |          0 |       3 | hash       |           37144 |           0 |      9350272 |          0 |      292196
   193929 |                    5 |         0 |          0 |       3 | hash       |            9492 |           0 |        23360 |          0 |        1460
   193929 |                    1 |         0 |          0 |       3 | hash       |           46809 |           0 |      9350272 |          0 |      292196
   193929 |                    4 |         0 |          0 |       2 | return     |            7685 |           0 |          896 |          0 |         112
   193929 |                    1 |         0 |          0 |       2 | project    |           46809 |           0 |            0 |          0 |      292196
   193929 |                    2 |         0 |          0 |       2 | project    |           37144 |           0 |            0 |          0 |      292196
   193929 |                    5 |         0 |          0 |       2 | project    |            9492 |           0 |            0 |          0 |        1460
   193929 |                    3 |         0 |          0 |       2 | return     |           11033 |           0 |        14336 |          0 |         112
   193929 |                    2 |         0 |          0 |       1 | project    |           37144 |           0 |            0 |          0 |      292196
   193929 |                    1 |         0 |          0 |       1 | project    |           46809 |           0 |            0 |          0 |      292196
   193929 |                    5 |         0 |          0 |       1 | project    |            9492 |           0 |            0 |          0 |        1460
   193929 |                    3 |         0 |          0 |       1 | aggregate  |           11033 |           0 |       201488 |          0 |          14
   193929 |                    4 |         0 |          0 |       1 | aggregate  |            7685 |           0 |        28784 |          0 |          14
   193929 |                    5 |         0 |          0 |       0 | scan       |            9492 |           0 |        23360 |     292196 |        1460
   193929 |                    4 |         0 |          0 |       0 | scan       |            7685 |           0 |         1344 |        112 |         112
   193929 |                    2 |         0 |          0 |       0 | scan       |           37144 |           0 |      7304900 |     292196 |      292196
   193929 |                    3 |         0 |          0 |       0 | scan       |           11033 |           0 |        13440 |        112 |         112
   193929 |                    1 |         0 |          0 |       0 | scan       |           46809 |           0 |      7304900 |     292196 |      292196
   193929 |                    5 |         0 |          0 |      -1 |            |            9492 |       12288 |            0 |          0 |           0
   193929 |                    1 |         0 |          0 |      -1 |            |           46809 |       16384 |            0 |          0 |           0
   193929 |                    2 |         0 |          0 |      -1 |            |           37144 |       16384 |            0 |          0 |           0
   193929 |                    4 |         0 |          0 |      -1 |            |            7685 |       28672 |            0 |          0 |           0
   193929 |                    3 |         0 |          0 |      -1 |            |           11033 |      114688 |            0 |          0 |           0
```