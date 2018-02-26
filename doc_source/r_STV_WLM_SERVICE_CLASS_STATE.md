# STV\_WLM\_SERVICE\_CLASS\_STATE<a name="r_STV_WLM_SERVICE_CLASS_STATE"></a>

Contains the current state of the service classes\. 

STV\_WLM\_SERVICE\_CLASS\_STATE is visible only to superusers\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STV_WLM_SERVICE_CLASS_STATE-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_SERVICE_CLASS_STATE.html)

## Sample Query<a name="r_STV_WLM_SERVICE_CLASS_STATE-sample-query2"></a>

Service classes 1 \- 4 are used internally by Amazon Redshift, and service class 5 is reserved for the dedicated superuser queue\. The following query displays the state for service classes greater than 5, which are the WLM query queues\. 

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