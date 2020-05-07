# WLM dynamic memory allocation<a name="cm-c-wlm-dynamic-memory-allocation"></a>

In each queue, WLM creates a number of query slots equal to the queue's concurrency level\. The amount of memory allocated to a query slot equals the percentage of memory allocated to the queue divided by the slot count\. If you change the memory allocation or concurrency, Amazon Redshift dynamically manages the transition to the new WLM configuration\. Thus, active queries can run to completion using the currently allocated amount of memory\. At the same time, Amazon Redshift ensures that total memory usage never exceeds 100 percent of available memory\.

The workload manager uses the following process to manage the transition:

1. WLM recalculates the memory allocation for each new query slot\. 

1. If a query slot is not actively being used by a running query, WLM removes the slot, which makes that memory available for new slots\. 

1. If a query slot is actively in use, WLM waits for the query to finish\. 

1. As active queries complete, the empty slots are removed and the associated memory is freed\. 

1. As enough memory becomes available to add one or more slots, new slots are added\. 

1. When all queries that were running at the time of the change finish, the slot count equals the new concurrency level, and the transition to the new WLM configuration is complete\.

In effect, queries that are running when the change takes place continue to use the original memory allocation\. Queries that are queued when the change takes place are routed to new slots as they become available\. 

If the WLM dynamic properties are changed during the transition process, WLM immediately begins to transition to the new configuration, starting from the current state\. To view the status of the transition, query the [STV\_WLM\_SERVICE\_CLASS\_CONFIG](r_STV_WLM_SERVICE_CLASS_CONFIG.md) system table\. 