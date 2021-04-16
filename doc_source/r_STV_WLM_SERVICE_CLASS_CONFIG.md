# STV\_WLM\_SERVICE\_CLASS\_CONFIG<a name="r_STV_WLM_SERVICE_CLASS_CONFIG"></a>

Records the service class configurations for WLM\. 

STV\_WLM\_SERVICE\_CLASS\_CONFIG is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_SERVICE_CLASS_CONFIG-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_SERVICE_CLASS_CONFIG.html)

## Sample query<a name="r_STV_WLM_SERVICE_CLASS_CONFIG-sample-query2"></a>

The first user\-defined service class is service class 6, which is named Service class \#1\. The following query displays the current configuration for service classes greater than 4\. For a list of service class IDs, see [WLM service class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\. 

```
select rtrim(name) as name, 
num_query_tasks as slots, 
query_working_mem as mem, 
max_execution_time as max_time, 
user_group_wild_card as user_wildcard, 
query_group_wild_card as query_wildcard
from stv_wlm_service_class_config
where service_class > 4;

name                         | slots | mem | max_time | user_wildcard | query_wildcard
-----------------------------+-------+-----+----------+---------------+---------------
Service class for super user |     1 | 535 |        0 | false         | false   
Queue 1                      |     5 | 125 |        0 | false         | false         
Queue 2                      |     5 | 125 |        0 | false         | false         
Queue 3                      |     5 | 125 |        0 | false         | false         
Queue 4                      |     5 | 627 |        0 | false         | false         
Queue 5                      |     5 | 125 |        0 | true          | true          
Default queue                |     5 | 125 |        0 | false         | false
```

The following query shows the status of a dynamic WLM transition\. While the transition is in process, `num_query_tasks` and `target_query_working_mem` are updated until they equal the target values\. For more information, see [WLM dynamic and static configuration properties](cm-c-wlm-dynamic-properties.md)\.

```
select rtrim(name) as name, 
num_query_tasks as slots, 
target_num_query_tasks as target_slots, 
query_working_mem as memory, 
target_query_working_mem as target_memory
from stv_wlm_service_class_config
where num_query_tasks > target_num_query_tasks
or query_working_mem > target_query_working_mem
and service_class > 5;

 name             | slots | target_slots | memory | target_mem 
------------------+-------+--------------+--------+------------
 Queue 3          |     5 |           15 |    125 |       375 
 Queue 5          |    10 |            5 |    250 |       125 
 (2 rows)
```