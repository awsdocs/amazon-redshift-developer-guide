# WLM Dynamic and Static Configuration Properties<a name="cm-c-wlm-dynamic-properties"></a>

The WLM configuration properties are either dynamic or static\. If you change any of the dynamic properties, you don't need to reboot your cluster for the changes to take effect\. While dynamic changes are being applied, your cluster status is modifying\. If you add or remove query queues, switch to automatic or manual WLM, or change any static properties, restart your cluster\. You need to perform this restart before any WLM parameter changes take effect, including changes to dynamic properties\. 

The following WLM properties are static: 
+ Query group wildcard 
+ Query groups 
+ User group wildcard 
+ User groups 

The following WLM properties are dynamic: 
+ Concurrency
+ Concurrency Scaling mode
+ Enable short query acceleration
+ Maximum run time for short queries
+ Percent of memory to use
+ Timeout

If the timeout value is changed, the new value is applied to any query that begins execution after the value is changed\. If the concurrency or percent of memory to use are changed, Amazon Redshift transitions to the new configuration dynamically\. Thus, currently running queries aren't affected by the change\. For more information, see [WLM Dynamic Memory Allocation](cm-c-wlm-dynamic-memory-allocation.md)\.

**Topics**
+ [WLM Dynamic Memory Allocation](cm-c-wlm-dynamic-memory-allocation.md)
+ [Dynamic WLM Example](cm-c-wlm-dynamic-example.md)