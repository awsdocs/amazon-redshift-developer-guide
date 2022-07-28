# STL\_COMMIT\_STATS<a name="r_STL_COMMIT_STATS"></a>

Provides metrics related to commit performance, including the timing of the various stages of commit and the number of blocks committed\. Query STL\_COMMIT\_STATS to determine what portion of a transaction was spent on commit and how much queuing is occurring\.

This view is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_COMMIT_STATS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_COMMIT_STATS.html)

## Sample query<a name="r_STL_COMMIT_STATS-sample-queries"></a>

```
select node, datediff(ms,startqueue,startwork) as queue_time, 
datediff(ms, startwork, endtime) as commit_time, queuelen
from stl_commit_stats
where xid = 2574
order by node;

node | queue_time   | commit_time | queuelen
-----+--------------+-------------+---------
  -1 |            0 |         617 |        0
   0 | 444950725641 |         616 |        0
   1 | 444950725636 |         616 |        0
```