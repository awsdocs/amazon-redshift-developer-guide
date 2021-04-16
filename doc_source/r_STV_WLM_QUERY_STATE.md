# STV\_WLM\_QUERY\_STATE<a name="r_STV_WLM_QUERY_STATE"></a>

Records the current state of queries being tracked by WLM\. 

STV\_WLM\_QUERY\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_QUERY_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QUERY_STATE.html)

## Sample query<a name="r_STV_WLM_QUERY_STATE-sample-query"></a>

The following query displays all currently executing queries in service classes greater than 4\. For a list of service class IDs, see [WLM service class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\.

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