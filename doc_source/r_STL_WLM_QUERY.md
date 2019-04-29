# STL\_WLM\_QUERY<a name="r_STL_WLM_QUERY"></a>

Contains a record of each attempted execution of a query in a service class handled by WLM\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_WLM_QUERY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_WLM_QUERY.html)

## Sample Queries<a name="r_STL_WLM_QUERY-sample-queries"></a>

 **View Average Query Time in Queues and Executing** 

The following queries display the current configuration for service classes greater than 4\. For a list of service class IDs, see [WLM Service Class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\.

The following query returns the average time \(in microseconds\) that each query spent in query queues and executing for each service class\. 

```
select service_class as svc_class, count(*),
avg(datediff(microseconds, queue_start_time, queue_end_time)) as avg_queue_time,
avg(datediff(microseconds, exec_start_time, exec_end_time )) as avg_exec_time
from stl_wlm_query
where service_class > 4
group by service_class
order by service_class;
```

This query returns the following sample output: 

```
 svc_class | count | avg_queue_time | avg_exec_time
-----------+-------+----------------+---------------
         5 | 20103 |              0 |         80415
         5 |  3421 |          34015 |        234015
         6 |    42 |              0 |        944266
         7 |   196 |           6439 |       1364399
(4 rows)
```

 **View Maximum Query Time in Queues and Executing** 

The following query returns the maximum amount of time \(in microseconds\) that a query spent in any query queue and executing for each service class\.

```
select service_class as svc_class, count(*),
max(datediff(microseconds, queue_start_time, queue_end_time)) as max_queue_time,
max(datediff(microseconds, exec_start_time, exec_end_time )) as max_exec_time
from stl_wlm_query
where svc_class > 5  
group by service_class
order by service_class;
```

```
 svc_class | count | max_queue_time | max_exec_time
-----------+-------+----------------+---------------
         6 |    42 |              0 |       3775896
         7 |   197 |          37947 |      16379473
(4 rows)
```