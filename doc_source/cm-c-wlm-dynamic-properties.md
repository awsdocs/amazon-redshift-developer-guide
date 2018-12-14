# WLM Dynamic and Static Configuration Properties<a name="cm-c-wlm-dynamic-properties"></a>

The WLM configuration properties are either dynamic or static\. If you change any of the dynamic properties, you don’t need to reboot your cluster for the changes to take effect\. While dynamic changes are being applied, your cluster status is modifying\. If you add or remove query queues or change any of the static properties, you must restart your cluster before any WLM parameter changes, including changes to dynamic properties, take effect\. 

The following WLM properties are static: 
+ User groups 
+ User group wildcard 
+ Query groups 
+ Query group wildcard 

The following WLM properties are dynamic: 
+ Concurrency
+ Percent of memory to use
+ Timeout

If the timeout value is changed, the new value is applied to any query that begins execution after the value is changed\. If the concurrency or percent of memory to use are changed, Amazon Redshift transitions to the new configuration dynamically so that currently running queries are not affected by the change\. For more information, see [WLM Dynamic Memory Allocation](cm-c-wlm-dynamic-memory-allocation.md)\.

**Topics**
+ [WLM Dynamic Memory Allocation](cm-c-wlm-dynamic-memory-allocation.md)
+ [Dynamic WLM Example](cm-c-wlm-dynamic-example.md)