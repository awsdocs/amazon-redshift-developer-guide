# STV\_WLM\_QUERY\_STATE<a name="r_STV_WLM_QUERY_STATE"></a>

Records the current state of queries being tracked by WLM\. 

STV\_WLM\_QUERY\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STV_WLM_QUERY_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QUERY_STATE.html)

## Sample Query<a name="r_STV_WLM_QUERY_STATE-sample-query"></a>

Service classes 1 \- 4 are used internally by Amazon Redshift, and service class 5 is reserved for the dedicated superuser queue\. The following query displays all currently executing queries in service classes greater than 4, which are the superuser queue and the WLM query queues\.

```
select xid, query, trim(state), queue_time, exec_time
from stv_wlm_query_state
where service_class > 4;
```

This query returns the following sample output: 

```
xid    | query | btrim   | queue_time | exec_time 
-------+-------+---------+------------+-----------
100813 | 25942 | Running |          0 |    1369029
100074 | 25775 | Running |          0 | 2221589242
```