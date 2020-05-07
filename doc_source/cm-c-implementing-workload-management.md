# Implementing workload management<a name="cm-c-implementing-workload-management"></a>

You can use workload management \(WLM\) to define multiple query queues and to route queries to the appropriate queues at runtime\.

In some cases, you might have multiple sessions or users running queries at the same time\. In these cases, some queries might consume cluster resources for long periods of time and affect the performance of other queries\. For example, suppose that one group of users submits occasional complex, long\-running queries that select and sort rows from several large tables\. Another group frequently submits short queries that select only a few rows from one or two tables and run in a few seconds\. In this situation, the short\-running queries might have to wait in a queue for a long\-running query to complete\. WLM helps manage this situation\.

You can configure Amazon Redshift WLM to run with either automatic WLM or manual WLM\.
+ Automatic WLM

  To maximize system throughput and use resources effectively, you can enable Amazon Redshift to manage how resources are divided to run concurrent queries with automatic WLM\. *Automatic WLM* manages the resources required to run queries\. Amazon Redshift determines how many queries run concurrently and how much memory is allocated to each dispatched query\. You can enable automatic WLM using the Amazon Redshift console by choosing **Switch WLM mode** and then choosing **Auto WLM**\. With this choice, up to eight queues are used to manage queries, and the **Memory** and **Concurrency on main** fields are both set to **Auto**\. You can specify a priority that reflects the business priority of the workload or users that map to each queue\. The default priority of queries is set to **Normal**\. For information about how to change the priority of queries in a queue, see [Query priority](query-priority.md)\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\. 

  At runtime, you can route queries to these queues according to user groups or query groups\. You can also configure a query monitoring rule \(QMR\) to limit long\-running queries\. 

  Working with concurrency scaling and automatic WLM, you can support virtually unlimited concurrent users and concurrent queries, with consistently fast query performance\. For more information, see [Working with concurrency scaling](concurrency-scaling.md)\.
**Note**  
We recommend that you create a parameter group and choose automatic WLM to manage your query resources\. For details about how to migrate from manual WLM to automatic WLM, see [Migrating from manual WLM to automatic WLM](cm-c-modifying-wlm-configuration.md#wlm-manual-to-automatic)\. 
+ Manual WLM

  Alternatively, you can manage system performance and your users' experience by modifying your WLM configuration to create separate queues for the long\-running queries and the short\-running queries\. At runtime, you can route queries to these queues according to user groups or query groups\. You can enable this manual configuration using the Amazon Redshift console by switching to **Manual WLM**\. With this choice, you specify the queues used to manage queries, and the **Memory** and **Concurrency on main** field values\. With a manual configuration, you can configure up to eight query queues and set the number of queries that can run in each of those queues concurrently\. You can set up rules to route queries to particular queues based on the user running the query or labels that you specify\. You can also configure the amount of memory allocated to each queue, so that large queries run in queues with more memory than other queues\. You can also configure a query monitoring rule \(QMR\) to limit long\-running queries\. For more information, see [Implementing manual WLM](cm-c-defining-query-queues.md)\. 
**Note**  
We recommend configuring your manual WLM query queues with a total of 15 or fewer query slots\. For more information, see [Concurrency level](cm-c-defining-query-queues.md#cm-c-defining-query-queues-concurrency-level)\.

**Topics**
+ [Modifying the WLM configuration](cm-c-modifying-wlm-configuration.md)
+ [Implementing automatic WLM](automatic-wlm.md)
+ [Implementing manual WLM](cm-c-defining-query-queues.md)
+ [Working with concurrency scaling](concurrency-scaling.md)
+ [Working with short query acceleration](wlm-short-query-acceleration.md)
+ [WLM queue assignment rules](cm-c-wlm-queue-assignment-rules.md)
+ [Assigning queries to queues](cm-c-executing-queries.md)
+ [WLM dynamic and static configuration properties](cm-c-wlm-dynamic-properties.md)
+ [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)
+ [WLM system tables and views](cm-c-wlm-system-tables-and-views.md)