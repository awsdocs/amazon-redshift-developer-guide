# Dynamic WLM example<a name="cm-c-wlm-dynamic-example"></a>

Suppose that your cluster WLM is configured with two queues, using the following dynamic properties\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-dynamic-example.html)

Now suppose that your cluster has 200 GB of memory available for query processing\. \(This number is arbitrary and used for illustration only\.\) As the following equation shows, each slot is allocated 25 GB\. 

```
(200 GB * 50% ) / 4 slots  = 25 GB
```

Next, you change your WLM to use the following dynamic properties\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-dynamic-example.html)

As the following equation shows, the new memory allocation for each slot in queue 1 is 50 GB\. 

```
(200 GB * 75% ) / 3 slots = 50 GB 
```

Suppose that queries A1, A2, A3, and A4 are running when the new configuration is applied, and queries B1, B2, B3, and B4 are queued\. WLM dynamically reconfigures the query slots as follows\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-dynamic-example.html)

1. WLM recalculates the memory allocation for each query slot\. Originally, queue 1 was allocated 100 GB\. The new queue has a total allocation of 150 GB, so the new queue immediately has 50 GB available\. Queue 1 is now using four slots, and the new concurrency level is three slots, so no new slots are added\. 

1. When one query finishes, the slot is removed and 25 GB is freed\. Queue 1 now has three slots and 75 GB of available memory\. The new configuration needs 50 GB for each new slot, but the new concurrency level is three slots, so no new slots are added\. 

1. When a second query finishes, the slot is removed, and 25 GB is freed\. Queue 1 now has two slots and 100 GB of free memory\. 

1. A new slot is added using 50 GB of the free memory\. Queue 1 now has three slots, and 50 GB free memory\. Queued queries can now be routed to the new slot\. 

1. When a third query finishes, the slot is removed, and 25 GB is freed\. Queue 1 now has two slots, and 75 GB of free memory\. 

1. A new slot is added using 50 GB of the free memory\. Queue 1 now has three slots, and 25 GB free memory\. Queued queries can now be routed to the new slot\. 

1. When the fourth query finishes, the slot is removed, and 25 GB is freed\. Queue 1 now has two slots and 50 GB of free memory\. 

1. A new slot is added using the 50 GB of free memory\. Queue 1 now has three slots with 50 GB each and all available memory has been allocated\. 

The transition is complete and all query slots are available to queued queries\.