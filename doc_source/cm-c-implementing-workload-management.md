# Implementing Workload Management<a name="cm-c-implementing-workload-management"></a>

You can use workload management \(WLM\) to define multiple query queues and to route queries to the appropriate queues at runtime\.

When you have multiple sessions or users running queries at the same time, some queries might consume cluster resources for long periods of time and affect the performance of other queries\. For example, suppose one group of users submits occasional complex, long\-running queries that select and sort rows from several large tables\. Another group frequently submits short queries that select only a few rows from one or two tables and run in a few seconds\. In this situation, the short\-running queries might have to wait in a queue for a long\-running query to complete\.

You can improve system performance and your users’ experience by modifying your WLM configuration to create separate queues for the long\-running queries and the short\-running queries\. At run time, you can route queries to these queues according to user groups or query groups\.

You can configure up to eight query queues and set the number of queries that can run in each of those queues concurrently\. You can set up rules to route queries to particular queues based on the user running the query or labels that you specify\. You can also configure the amount of memory allocated to each queue, so that large queries run in queues with more memory than other queues\. You can also configure the WLM timeout property to limit long\-running queries\. 

With Concurrency Scaling, you can support virtually unlimited concurrent users and concurrent queries, with consistently fast query performance\. 

**Note**  
We recommend configuring your WLM query queues with a total of 15 or fewer query slots\. For more information, see [Concurrency Level](cm-c-defining-query-queues.md#cm-c-defining-query-queues-concurrency-level)

**Topics**
+ [Defining Query Queues](cm-c-defining-query-queues.md)
+ [Concurrency Scaling](concurrency-scaling.md)
+ [WLM Query Queue Hopping](wlm-queue-hopping.md)
+ [Short Query Acceleration](wlm-short-query-acceleration.md)
+ [Modifying the WLM Configuration](cm-c-modifying-wlm-configuration.md)
+ [WLM Queue Assignment Rules](cm-c-wlm-queue-assignment-rules.md)
+ [Assigning Queries to Queues](cm-c-executing-queries.md)
+ [WLM Dynamic and Static Configuration Properties](cm-c-wlm-dynamic-properties.md)
+ [WLM Query Monitoring Rules](cm-c-wlm-query-monitoring-rules.md)
+ [WLM System Tables and Views](cm-c-wlm-system-tables-and-views.md)