# SVV\_QUERY\_INFLIGHT<a name="r_SVV_QUERY_INFLIGHT"></a>

Use the SVV\_QUERY\_INFLIGHT view to determine what queries are currently running on the database\. This view joins [STV\_INFLIGHT](r_STV_INFLIGHT.md) to [STL\_QUERYTEXT](r_STL_QUERYTEXT.md)\. SVV\_QUERY\_INFLIGHT does not show leader\-node only queries\. For more information, see [Leader node–only functions](c_SQL_functions_leader_node_only.md)\.

SVV\_QUERY\_INFLIGHT is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="sub-r_SVV_QUERY_INFLIGHT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_QUERY_INFLIGHT.html)

## Sample queries<a name="r_SVV_QUERY_INFLIGHT-sample-queries"></a>

The sample output below shows two queries currently running, the SVV\_QUERY\_INFLIGHT query itself and query 428, which is split into three rows in the table\. \(The starttime and statement columns are truncated in this sample output\.\) 

```
select slice, query, pid, starttime, suspended, trim(text) as statement, sequence
from svv_query_inflight
order by query, sequence;

slice|query| pid  |      starttime       |suspended| statement | sequence
-----+-----+------+----------------------+---------+-----------+---------
1012 | 428 | 1658 | 2012-04-10 13:53:... |       0 | select ...|    0
1012 | 428 | 1658 | 2012-04-10 13:53:... |       0 | enueid ...|    1
1012 | 428 | 1658 | 2012-04-10 13:53:... |       0 | atname,...|    2
1012 | 429 | 1608 | 2012-04-10 13:53:... |       0 | select ...|    0
(4 rows)
```