# CHANGE\_QUERY\_PRIORITY<a name="r_CHANGE_QUERY_PRIORITY"></a>

CHANGE\_QUERY\_PRIORITY enables superusers to modify the priority of a query that is either running or waiting in workload management \(WLM\)\.

This function enables superusers to immediately change the priority of any query in the system\. Only one query, user, or session can run with the priority `CRITICAL`\.

## Syntax<a name="r_CHANGE_QUERY_PRIORITY-synopsis"></a>

```
CHANGE_QUERY_PRIORITY(query_id, priority)
```

## Arguments<a name="r_CHANGE_QUERY_PRIORITY-argument"></a>

 *query\_id*   
The query identifier of the query whose priority is changed\.

 *priority*   
The new priority to be assigned to the query\. This argument must be a string with the value `CRITICAL`, `HIGHEST`, `HIGH`, `NORMAL`, `LOW`, or `LOWEST`\. 

## Return Type<a name="r_CHANGE_QUERY_PRIORITY-return-type"></a>

None

## Example<a name="r_CHANGE_QUERY_PRIORITY-example"></a>

The following example shows the column `query_priority` in the STV\_WLM\_QUERY\_STATE system table\.

```
select query, service_class, query_priority, state 
from stv_wlm_query_state where service_class = 101;
 query | service_class | query_priority       | state
-------+---------------+----------------------+------------------
 1076  | 101           | Lowest               | Running
 1075  | 101           | Lowest               | Running
(2 rows)
```

The following example shows the results of a superuser running the function `change_query_priority` to change the priority to `CRITICAL`\.

```
select change_query_priority(1076, 'Critical');
            
 change_query_priority
--------------------------------------------------
Succeeded to change query priority. Priority changed from Lowest to Critical.
(1 row)
```