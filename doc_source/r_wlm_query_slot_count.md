# wlm\_query\_slot\_count<a name="r_wlm_query_slot_count"></a>

## Values \(default in bold\)<a name="r_wlm_query_slot_count-values-"></a>

 **1**, 1 to 50 \(cannot exceed number of available slots \(concurrency level\) for the service class\)

## Description<a name="r_wlm_query_slot_count-description"></a>

Sets the number of query slots a query uses\.

Workload management \(WLM\) reserves slots in a service class according to the concurrency level set for the queue \(for example, if concurrency level is set to 5, then the service class has 5 slots\)\. WLM allocates the available memory for a service class equally to each slot\. For more information, see [Implementing workload management](cm-c-implementing-workload-management.md)\. 

**Note**  
If the value of wlm\_query\_slot\_count is larger than the number of available slots \(concurrency level\) for the service class, the query fails\. If you encounter an error, decrease wlm\_query\_slot\_count to an allowable value\.

 For operations where performance is heavily affected by the amount of memory allocated, such as Vacuum, increasing the value of wlm\_query\_slot\_count can improve performance\. In particular, for slow Vacuum commands, inspect the corresponding record in the SVV\_VACUUM\_SUMMARY view\. If you see high values \(close to or higher than 100\) for sort\_partitions and merge\_increments in the SVV\_VACUUM\_SUMMARY view, consider increasing the value for wlm\_query\_slot\_count the next time you run Vacuum against that table\.

Increasing the value of wlm\_query\_slot\_count limits the number of concurrent queries that can be run\. For example, suppose the service class has a concurrency level of 5 and wlm\_query\_slot\_count is set to 3\. While a query is running within the session with wlm\_query\_slot\_count set to 3, a maximum of 2 more concurrent queries can be executed within the same service class\. Subsequent queries wait in the queue until currently executing queries complete and slots are freed\.

## Examples<a name="r_wlm_query_slot_count-examples"></a>

Use the SET command to set the value of wlm\_query\_slot\_count for the duration of the current session\. 

```
set wlm_query_slot_count to 3; 
```