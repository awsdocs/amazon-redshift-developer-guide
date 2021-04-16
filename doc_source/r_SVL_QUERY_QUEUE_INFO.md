# SVL\_QUERY\_QUEUE\_INFO<a name="r_SVL_QUERY_QUEUE_INFO"></a>

Summarizes details for queries that spent time in a workload management \(WLM\) query queue or a commit queue\. 

The SVL\_QUERY\_QUEUE\_INFO view filters queries executed by the system and shows only queries executed by a user\. 

The SVL\_QUERY\_QUEUE\_INFO view summarizes information from the [STL\_QUERY](r_STL_QUERY.md), [STL\_WLM\_QUERY](r_STL_WLM_QUERY.md), and [STL\_COMMIT\_STATS](r_STL_COMMIT_STATS.md) system tables\. 

This view is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_QUERY_QUEUE_INFO-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_QUERY_QUEUE_INFO.html)

## Sample queries<a name="r_SVL_QUERY_QUEUE_INFO-sample-queries"></a>

The following example shows the time that queries spent in WLM queues\.

```
select query, service_class, queue_elapsed, exec_elapsed, wlm_total_elapsed
from svl_query_queue_info
where wlm_total_elapsed > 0;

  query  | service_class | queue_elapsed | exec_elapsed | wlm_total_elapsed
---------+---------------+---------------+--------------+-------------------
 2742669 |             6 |             2 |          916 |                918 
 2742668 |             6 |             4 |          197 |                201 
(2 rows)
```