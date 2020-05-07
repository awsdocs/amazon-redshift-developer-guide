# Implementing manual WLM<a name="cm-c-defining-query-queues"></a>

With manual WLM, you can manage system performance and your users' experience by modifying your WLM configuration to create separate queues for the long\-running queries and the short\-running queries\.

When users run queries in Amazon Redshift, the queries are routed to query queues\. Each query queue contains a number of query slots\. Each queue is allocated a portion of the cluster's available memory\. A queue's memory is divided among the queue's query slots\. You can enable Amazon Redshift to manage query concurrency with automatic WLM\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\.

Or you can configure WLM properties for each query queue\. You do so to specify the way that memory is allocated among slots and how queries can be routed to specific queues at run\. You can also configure WLM properties to cancel long\-running queries\. In addition, you can use the `wlm_query_slot_count` parameter, which is separate from the WLM properties\. This parameter can temporarily enable queries to use more memory by allocating multiple slots\. 

By default, Amazon Redshift configures the following query queues:
+  **One superuser queue** 

  The superuser queue is reserved for superusers only and it can't be configured\. Use this queue only when you need to run queries that affect the system or for troubleshooting purposes\. For example, use this queue when you need to cancel a user's long\-running query or to add users to the database\. Don't use it to perform routine queries\. The queue doesn't appear in the console, but it does appear in the system tables in the database as the fifth queue\. To run a query in the superuser queue, a user must be logged in as a superuser, and must run the query using the predefined `superuser` query group\.
+  **One default user queue** 

  The default queue is initially configured to run five queries concurrently\. You can change the concurrency, timeout, and memory allocation properties for the default queue, but you cannot specify user groups or query groups\. The default queue must be the last queue in the WLM configuration\. Any queries that are not routed to other queues run in the default queue\. 

Query queues are defined in the WLM configuration\. The WLM configuration is an editable parameter \(`wlm_json_configuration`\) in a parameter group, which can be associated with one or more clusters\. For more information, For more information, see [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) in the *Amazon Redshift Cluster Management Guide*\.  

You can add additional query queues to the default WLM configuration, up to a total of eight user queues\. You can configure the following for each query queue: 
+ Concurrency scaling mode 
+ Concurrency level 
+ User groups 
+ Query groups 
+ WLM memory percent to use
+ WLM timeout
+ WLM query queue hopping
+ Query monitoring rules

## Concurrency scaling mode<a name="concurrency-scaling-mode"></a>

When concurrency scaling is enabled, Amazon Redshift automatically adds additional cluster capacity when you need it to process an increase in concurrent read queries\. Write operations continue as normal on your main cluster\. Users see the most current data, whether the queries run on the main cluster or on a concurrency scaling cluster\. 

You manage which queries are sent to the concurrency scaling cluster by configuring WLM queues\. When you enable concurrency scaling for a queue, eligible queries are sent to the concurrency scaling cluster instead of waiting in line\. For more information, see [Working with concurrency scaling](concurrency-scaling.md)\.

## Concurrency level<a name="cm-c-defining-query-queues-concurrency-level"></a>

Queries in a queue run concurrently until they reach the WLM query slot count, or *concurrency* level, defined for that queue\. Subsequent queries then wait in the queue\. 

**Note**  
WLM concurrency level is different from the number of concurrent user connections that can be made to a cluster\.  For more information, see [Connecting to a Cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-to-cluster.html) in the *Amazon Redshift Cluster Management Guide*\.

In an automatic WLM configuration \(recommended\), the concurrency level is set to **Auto**\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\. 

In a manual WLM configuration, each queue can be configured with up to 50 query slots\. The maximum WLM query slot count for all user\-defined queues is 50\. The limit includes the default queue, but doesn't include the reserved superuser queue\. By default, Amazon Redshift allocates an equal, fixed share of available memory to each queue\. Amazon Redshift also allocates by default an equal, fixed share of a queue's memory to each query slot in the queue\. The proportion of memory allocated to each queue is defined in the WLM configuration using the `memory_percent_to_use` property\. At runtime, you can temporarily override the amount of memory assigned to a query by setting the `wlm_query_slot_count` parameter to specify the number of slots allocated to the query\.

By default, manual WLM queues have a concurrency level of 5\. Your workload might benefit from a higher concurrency level in certain cases, such as the following:
+ If many small queries are forced to wait for long\-running queries, create a separate queue with a higher slot count and assign the smaller queries to that queue\. A queue with a higher concurrency level has less memory allocated to each query slot, but the smaller queries require less memory\.
**Note**  
If you enable short\-query acceleration \(SQA\), WLM automatically prioritizes short queries over longer\-running queries, so you don't need a separate queue for short queries for most workflows\. For more information, see [Working with short query acceleration](wlm-short-query-acceleration.md)\. 
+ If you have multiple queries that each access data on a single slice, set up a separate WLM queue to execute those queries concurrently\. Amazon Redshift assigns concurrent queries to separate slices, which allows multiple queries to execute in parallel on multiple slices\. For example, if a query is a simple aggregate with a predicate on the distribution key, the data for the query is located on a single slice\. 

If your workload requires more than 15 queries to run in parallel, then we recommend enabling concurrency scaling\. This is because increasing query slot count above 15 might create contention for system resources and limit the overall throughput of a single cluster\. With concurrency scaling, you can run hundreds of queries in parallel up to a configured number of concurrency scaling clusters\. The number of concurrency scaling clusters that can be used is controlled by [max\_concurrency\_scaling\_clusters](r_max_concurrency_scaling_clusters.md)\. For more information about concurrency scaling, see [Working with concurrency scaling](concurrency-scaling.md)\. 

The memory that is allocated to each queue is divided among the query slots in that queue\. The amount of memory available to a query is the memory allocated to the query slot in which the query is running\. This is true regardless of the number of queries that are actually running concurrently\. A query that can run entirely in memory when the slot count is 5 might need to write intermediate results to disk if the slot count is increased to 20\. The additional disk I/O could degrade performance\. 

If a specific query needs more memory than is allocated to a single query slot, you can increase the available memory by increasing the [wlm\_query\_slot\_count](r_wlm_query_slot_count.md) parameter\. The following example sets `wlm_query_slot_count` to 10, performs a vacuum, and then resets `wlm_query_slot_count` to 1\. 

```
set wlm_query_slot_count to 10; 
vacuum; 
set wlm_query_slot_count to 1;
```

For more information, see [Improving query performance](query-performance-improvement-opportunities.md)\. 

## User groups<a name="cm-c-defining-query-queues-user-groups"></a>

You can assign a set of user groups to a queue by specifying each user group name or by using wildcards\. When a member of a listed user group runs a query, that query runs in the corresponding queue\. There is no set limit on the number of user groups that can be assigned to a queue\. For more information, see [Wildcards](#wlm-wildcards)

## Query groups<a name="cm-c-defining-query-queues-query-groups"></a>

You can assign a set of query groups to a queue by specifying each query group name or by using wildcards\. A query group is simply a label\. At runtime, you can assign the query group label to a series of queries\. Any queries that are assigned to a listed query group run in the corresponding queue\. There is no set limit to the number of query groups that can be assigned to a queue\. For more information, see [Wildcards](#wlm-wildcards)

## Wildcards<a name="wlm-wildcards"></a>

If wildcards are enabled in the WLM queue configuration, you can assign user groups and query groups to a queue either individually or by using Unix shell\-style wildcards\. The pattern matching is case\-insensitive\. 

For example, the '\*' wildcard character matches any number of characters\. Thus, if you add `dba_*` to the list of user groups for a queue, any user\-run query that belongs to a group with a name that begins with `dba_` is assigned to that queue\. Examples are `dba_admin` or `DBA_primary`, \. The '?' wildcard character matches any single character\. Thus, if the queue includes user\-group `dba?1`, then user groups named `dba11` and `dba21` match, but `dba12` doesn't match\. 

Wildcards are disabled by default\.

## WLM memory percent to use<a name="wlm-memory-percent"></a>

In an automatic WLM configuration, memory percent is set to **auto**\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\. 

In a manual WLM configuration, to specify the amount of available memory that is allocated to a query, you can set the `WLM Memory Percent to Use` parameter\. By default, each user\-defined queue is allocated an equal portion of the memory that is available for user\-defined queries\. For example, if you have four user\-defined queues, each queue is allocated 25 percent of the available memory\. The superuser queue has its own allocated memory and cannot be modified\. To change the allocation, you assign an integer percentage of memory to each queue, up to a total of 100 percent\. Any unallocated memory is managed by Amazon Redshift and can be temporarily given to a queue if the queue requests additional memory for processing\. 

For example, if you configure four queues, you can allocate memory as follows: 20 percent, 30 percent, 15 percent, 15 percent\. The remaining 20 percent is unallocated and managed by the service\.

## WLM timeout<a name="wlm-timeout"></a>

WLM timeout \(`max_execution_time`\) is deprecated\. Instead, create a query monitoring rule \(QMR\) using `query_execution_time` to limit the elapsed execution time for a query\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\. 

To limit the amount of time that queries in a given WLM queue are permitted to use, you can set the WLM timeout value for each queue\. The timeout parameter specifies the amount of time, in milliseconds, that Amazon Redshift waits for a query to execute before either canceling or hopping the query\. The timeout is based on query execution time and doesn't include time spent waiting in a queue\. 

WLM attempts to hop [CREATE TABLE AS](r_CREATE_TABLE_AS.md) \(CTAS\) statements and read\-only queries, such as SELECT statements\. Queries that can't be hopped are canceled\. For more information, see [WLM query queue hopping](wlm-queue-hopping.md)\.

WLM timeout doesn't apply to a query that has reached the returning state\. To view the state of a query, see the [STV\_WLM\_QUERY\_STATE](r_STV_WLM_QUERY_STATE.md) system table\. COPY statements and maintenance operations, such as ANALYZE and VACUUM, are not subject to WLM timeout\.

The function of WLM timeout is similar to the [statement\_timeout](r_statement_timeout.md) configuration parameter\. The difference is that, where the `statement_timeout` configuration parameter applies to the entire cluster, WLM timeout is specific to a single queue in the WLM configuration\. 

If [statement\_timeout](r_statement_timeout.md) is also specified, the lower of statement\_timeout and WLM timeout \(max\_execution\_time\) is used\. 

## Query monitoring rules<a name="wlm-query-monitoring-rules"></a>

Query monitoring rules define metrics\-based performance boundaries for WLM queues and specify what action to take when a query goes beyond those boundaries\. For example, for a queue dedicated to short running queries, you might create a rule that aborts queries that run for more than 60 seconds\. To track poorly designed queries, you might have another rule that logs queries that contain nested loops\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.