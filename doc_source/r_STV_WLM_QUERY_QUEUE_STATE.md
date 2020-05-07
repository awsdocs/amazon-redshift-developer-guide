# STV\_WLM\_QUERY\_QUEUE\_STATE<a name="r_STV_WLM_QUERY_QUEUE_STATE"></a>

Records the current state of the query queues for the service classes\.

STV\_WLM\_QUERY\_QUEUE\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_QUERY_QUEUE_STATE-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QUERY_QUEUE_STATE.html)

## Sample query<a name="r_STV_WLM_QUERY_QUEUE_STATE-sample-query2"></a>

The following query shows the queries in the queue for service classes greater than 4\. 

```
select * from stv_wlm_query_queue_state
where service_class > 4
order by service_class;
```

 This query returns the following sample output\. 

```
 service_class | position | task | query | slot_count |        start_time          | queue_time
---------------+----------+------+-------+------------+----------------------------+------------
             5 |        0 |  455 |   476 |          5 | 2010-10-06 13:18:24.065838 |   20937257
             6 |        1 |  456 |   478 |          5 | 2010-10-06 13:18:26.652906 |   18350191
(2 rows)
```