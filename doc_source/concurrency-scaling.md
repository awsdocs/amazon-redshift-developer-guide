# Concurrency Scaling<a name="concurrency-scaling"></a>

With Concurrency Scaling, you can support virtually unlimited concurrent users and concurrent queries, with consistently fast query performance\. When Concurrency Scaling is enabled, Amazon Redshift automatically adds additional cluster capacity when you need it to process an increase in concurrent read queries\. Write operations continue as normal on your main cluster\. Users always see the most current data, whether the queries run on the main cluster or on a concurrency scaling cluster\. You're charged for concurrency scaling clusters only for the time they're in use\. For more information about pricing, see [Amazon Redshift pricing](https://aws.amazon.com/redshift/pricing/)\. You manage which queries are sent to the concurrency scaling cluster by configuring WLM queues\. When you enable Concurrency Scaling for a queue, eligible queries are sent to the concurrency scaling cluster instead of waiting in line\. 

## Concurrency Scaling Candidates<a name="concurrency-scaling-candidates"></a>

Queries are routed to the concurrency scaling cluster only when the main cluster meets the following requirements:
+ EC2\-VPC platform 
+ Node type must be dc2\.8xlarge, ds2\.8xlarge, dc2\.large, or ds2\.xlarge 
+ Maximum of 32 compute nodes 
+ Not a single\-node cluster 

A query must meet all the following criteria to be a candidate for Concurrency Scaling: 
+ The query must be a read\-only query\. 
+ The query doesn't reference tables that use an [interleaved sort key](t_Sorting_data.md#t_Sorting_data-interleaved)\. 
+ The query doesn't use Amazon Redshift Spectrum to reference external tables\. 
+ The query doesn't reference user\-defined temporary tables\. 

## Configuring Concurrency Scaling Queues<a name="concurrency-scaling-queues"></a>

You route queries to concurrency scaling clusters by enabling a workload manager \(WLM\) queue as a concurrency scaling queue\. To enable Concurrency Scaling on a queue, set the **Concurrency Scaling mode** value to **auto**\. 

When the number of queries routed to a concurrency scaling queue exceeds the queue's configured concurrency, eligible queries are sent to the concurrency scaling cluster\. When slots become available, queries are run on the main cluster\. The number of queues is limited only by the number of queues permitted per cluster\. As with any WLM queue, you route queries to a concurrency scaling queue based on user groups or by labeling queries with query group labels\. You can also route queries by defining [WLM Query Monitoring Rules](cm-c-wlm-query-monitoring-rules.md)\. For example, you might route all queries that take longer than 5 seconds to a concurrency scaling queue\. 

## Monitoring Concurrency Scaling<a name="concurrency-scaling-monitoring"></a>

You can see whether a query is running on the main cluster or a concurrency scaling cluster by viewing the Amazon Redshift console, navigating to **Cluster**, and choosing a cluster\. Then choose the **Queries** tab and view the values in the column **Executed on** to determine the cluster where the query ran\.

To find execution times, query the STL\_QUERY table and filter on the `concurrency_scaling_status` column\. The following query compares the queue time and execution time for queries run on the concurrency scaling cluster and queries run on the main cluster\.

```
SELECT w.service_class AS queue
     , q.concurrency_scaling_status
     , COUNT( * ) AS queries
     , SUM( q.aborted )  AS aborted
     , SUM( ROUND( total_queue_time::NUMERIC / 1000000,2 ) ) AS queue_secs
     , SUM( ROUND( total_exec_time::NUMERIC / 1000000,2 ) )  AS exec_secs
FROM stl_query q
     JOIN stl_wlm_query w
          USING (userid,query)
WHERE q.userid > 1
  AND q.starttime > '2019-01-04 16:38:00'
  AND q.endtime < '2019-01-04 17:40:00'
GROUP BY 1,2
ORDER BY 1,2;
```

## Concurrency Scaling System Views<a name="concurrency-scaling-monitoring-system-views"></a>

A set of system views with the prefix SVCS provide details from the system log tables about queries on both the main and concurrency scaling clusters\. 

The following views have similar information as the corresponding STL tables or SVL views: 
+ [SVCS\_ALERT\_EVENT\_LOG](r_SVCS_ALERT_EVENT_LOG.md) 
+ [SVCS\_COMPILE](r_SVCS_COMPILE.md) 
+ [SVCS\_EXPLAIN](r_SVCS_EXPLAIN.md) 
+ [SVCS\_PLAN\_INFO](r_SVCS_PLAN_INFO.md) 
+ [SVCS\_QUERY\_SUMMARY](r_SVCS_QUERY_SUMMARY.md) 
+ [SVCS\_STREAM\_SEGS](r_SVCS_STREAM_SEGS.md) 

The following views are specific to Concurrency Scaling\. 
+ [SVCS\_CONCURRENCY\_SCALING\_USAGE](r_SVCS_CONCURRENCY_SCALING_USAGE.md) 

For more information about Concurrency Scaling, see the following topics in the *Amazon Redshift Cluster Management Guide*\.
+ [Viewing Concurrency Scaling Data](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-concurrency-scaling.html) 
+ [Viewing Cluster Performance During Query Execution](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-query-cluster.html) 
+ [Viewing Query Details](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-query-execution-details.html) 