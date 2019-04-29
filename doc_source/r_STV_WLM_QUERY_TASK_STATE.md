# STV\_WLM\_QUERY\_TASK\_STATE<a name="r_STV_WLM_QUERY_TASK_STATE"></a>

Contains the current state of service class query tasks\. 

STV\_WLM\_QUERY\_TASK\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STV_WLM_QUERY_TASK_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QUERY_TASK_STATE.html)

## Sample Query<a name="r_STV_WLM_QUERY_TASK_STATE-sample-query"></a>

The following query displays the current state of queries in service classes greater than 4\. For a list of service class IDs, see [WLM Service Class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\.

```
select * from stv_wlm_query_task_state
where service_class > 4;
```

This query returns the following sample output: 

```
service_class | task | query |         start_time         | exec_time
--------------+------+-------+----------------------------+-----------
    5         |  466 |   491 | 2010-10-06 13:29:23.063787 | 357618748
(1 row)
```