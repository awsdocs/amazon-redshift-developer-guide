# STV\_QUERY\_METRICS<a name="r_STV_QUERY_METRICS"></a>

Contains metrics information, such as the number of rows processed, CPU usage, input/output, and disk use, for active queries running in user\-defined query queues \(service classes\)\. To view metrics for queries that have completed, see the [STL\_QUERY\_METRICS](r_STL_QUERY_METRICS.md) system table\. 

Query metrics are sampled at one second intervals\. As a result, different runs of the same query might return slightly different times\. Also, query segments that run in less than 1 second might not be recorded\. 

STV\_QUERY\_METRICS tracks and aggregates metrics at the query, segment, and step level\. For information about query segments and steps, see [Query planning and execution workflow](c-query-planning.md)\. Many metrics \(such as `max_rows`, `cpu_time`, and so on\) are summed across node slices\. For more information about node slices, see [Data warehouse system architecture](c_high_level_system_architecture.md)\. 

To determine the level at which the row reports metrics, examine the `segment` and `step_type` columns:
+ If both `segment` and `step_type` are `-1`, then the row reports metrics at the query level\. 
+ If `segment` is not `-1` and `step_type` is `-1`, then the row reports metrics at the segment level\. 
+ If both `segment` and `step_type` are not `-1`, then the row reports metrics at the step level\. 

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_QUERY_METRICS-table-rows2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_QUERY_METRICS.html)

## Step types<a name="r_STV_QUERY_METRICS-step-type"></a>

The following table lists step types relevant to database users\. The table doesn't list step types that are for internal use only\. If step type is \-1, the metric is not reported at the step level\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_QUERY_METRICS.html)

## Sample query<a name="r_STV_QUERY_METRICS-sample-query2"></a>

To find active queries with high CPU time \(more the 1,000 seconds\), run the following query\.

```
select query, cpu_time / 1000000 as cpu_seconds
from stv_query_metrics where segment = -1 and cpu_time > 1000000000
order by cpu_time;

query | cpu_seconds
------+------------
25775 |        9540
```

To find active queries with a nested loop join that returned more than one million rows, run the following query\.

```
select query, rows 
from stv_query_metrics 
where step_type = 15 and rows > 1000000
order by rows;

query | rows      
------+-----------
25775 | 1580225854
```

To find active queries that have run for more than 60 seconds and have used less than 10 seconds of CPU time, run the following query\. 

```
select query, run_time/1000000 as run_time_seconds
from stv_query_metrics 
where segment = -1 and run_time > 60000000 and cpu_time < 10000000;

query | run_time_seconds
------+-----------------
25775 |              114
```