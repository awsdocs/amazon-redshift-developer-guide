# Short Query Acceleration<a name="wlm-short-query-acceleration"></a>

Short query acceleration \(SQA\) prioritizes selected short\-running queries ahead of longer\-running queries\. SQA executes short\-running queries in a dedicated space, so that SQA queries aren't forced to wait in queues behind longer queries\. With SQA, short\-running queries begin executing more quickly and users see results sooner\. 

If you enable SQA, you can reduce or eliminate WLM queues that are dedicated to running short queries\. In addition, long\-running queries don't need to contend with short queries for slots in a queue, so you can configure your WLM queues to use fewer query slots\. When you use lower concurrency, query throughput is increased and overall system performance is improved for most workloads\. 

 [CREATE TABLE AS](r_CREATE_TABLE_AS.md) \(CTAS\) statements and read\-only queries, such as [SELECT](r_SELECT_synopsis.md) statements, are eligible for SQA\. Amazon Redshift uses a machine learning algorithm to analyze each eligible query and predict the query's execution time\. If the query's predicted execution time is less than the defined SQA maximum run time and the query would otherwise need to wait in a queue, then SQA separates the query from the WLM queues and schedules it for priority execution\. If a query runs longer than the SQA maximum run time, WLM moves the query to the first matching WLM queue based on the [WLM queue assignment rules](cm-c-wlm-queue-assignment-rules.md)\. Over time, predictions improve as SQA learns from your query patterns\. 

To enable SQA in the AWS console, edit the WLM configuration for a parameter group and choose **Enable short query acceleration**\. When you enable SQA, your total WLM query slot count, or *concurrency*, across all user\-defined queues must be 15 or fewer\. If you enable SQA using the AWS CLI or the Amazon Redshift API, the slot count limitation is not enforced\. As a best practice, we recommend using a WLM query slot count of 15 or fewer to maintain optimum overall system performance\. For information about modifying WLM configurations, see [Configuring Workload Management](http://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) in the *Amazon Redshift Cluster Management Guide*

## Maximum Run Time for Short Queries<a name="wlm-sqa-max-run-time"></a>

When you enable SQA, you can specify a maximum run time for short queries between 1 and 20 seconds\. The default value of 5 seconds works well for most use cases\.

If you think using different SQA maximum run time values might improve your system performance, analyze your workflow to find the maximum execution time for most of your short\-running queries\. The following query returns the maximum run time for queries at about the 70th percentile\. 

```
select least(greatest(percentile_cont(0.7) 
within group (order by total_exec_time / 1000000) + 2, 2), 20) 
from stl_wlm_query 
where userid >= 100
and final_state = 'Completed';
```

After you identify a maximum run time value that works well for your workload, you don't need to change it unless your workload changes significantly\.

## Monitoring SQA<a name="wlm-monitoring-sqa"></a>

To check whether SQA is enabled, run the following query\. If the query returns a row, then SQA is enabled\.

```
select * from stv_wlm_service_class_config 
where service_class = 14;
```

The following query shows the number of queries that went through each query queue \(service class\), along with average execution time, the number of queries with wait time at the 90th percentile, and the average wait time\. SQA queries use service class 14\.

```
select final_state, service_class, count(*), avg(total_exec_time), 
percentile_cont(0.9) within group (order by total_queue_time), avg(total_queue_time) 
from stl_wlm_query where userid >= 100 group by 1,2 order by 2,1;
```

To find which queries were picked up by SQA and completed successfully, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class = 14 and a.final_state = 'Completed' 
order by b.query desc limit 5;
```

To find queries that were picked up by SQA but timed out, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class = 14 and a.final_state = 'Evicted' 
order by b.query desc limit 5;
```