# STV\_WLM\_SERVICE\_CLASS\_STATE<a name="r_STV_WLM_SERVICE_CLASS_STATE"></a>

Contains the current state of the service classes\. 

STV\_WLM\_SERVICE\_CLASS\_STATE is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_SERVICE_CLASS_STATE-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_SERVICE_CLASS_STATE.html)

## Sample query<a name="r_STV_WLM_SERVICE_CLASS_STATE-sample-query2"></a>

The following query displays the state for service classes greater than 5\. For a list of service class IDs, see [WLM service class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\.

```
select service_class, num_executing_queries, 
num_executed_queries 
from stv_wlm_service_class_state 
where service_class > 5
order by service_class;
```

```
 service_class | num_executing_queries | num_executed_queries
---------------+-----------------------+----------------------
             6 |                     1 |                  222
             7 |                     0 |                  135
             8 |                     1 |                   39
(3 rows)
```