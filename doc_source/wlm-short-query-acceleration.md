# Working with short query acceleration<a name="wlm-short-query-acceleration"></a>

Short query acceleration \(SQA\) prioritizes selected short\-running queries ahead of longer\-running queries\. SQA runs short\-running queries in a dedicated space, so that SQA queries aren't forced to wait in queues behind longer queries\. SQA only prioritizes queries that are short\-running and are in a user\-defined queue\. With SQA, short\-running queries begin running more quickly and users see results sooner\. 

If you enable SQA, you can reduce or eliminate workload management \(WLM\) queues that are dedicated to running short queries\. In addition, long\-running queries don't need to contend with short queries for slots in a queue, so you can configure your WLM queues to use fewer query slots\. When you use lower concurrency, query throughput is increased and overall system performance is improved for most workloads\. 

 [CREATE TABLE AS](r_CREATE_TABLE_AS.md) \(CTAS\) statements and read\-only queries, such as [SELECT](r_SELECT_synopsis.md) statements, are eligible for SQA\.

Amazon Redshift uses a machine learning algorithm to analyze each eligible query and predict the query's execution time\. By default, WLM dynamically assigns a value for the SQA maximum runtime based on analysis of your cluster's workload\. Alternatively, you can specify a fixed value of 1–20 seconds\. In some cases, the query's predicted runtime might be less than the defined SQA maximum runtime\. In such cases, the query thus needs to wait in a queue\. Here, SQA separates the query from the WLM queues and schedules it for priority execution\. If a query runs longer than the SQA maximum runtime, WLM moves the query to the first matching WLM queue based on the [WLM queue assignment rules](cm-c-wlm-queue-assignment-rules.md)\. Over time, predictions improve as SQA learns from your query patterns\. 

SQA is enabled by default in the default parameter group and for all new parameter groups\. To disable SQA in the Amazon Redshift console, edit the WLM configuration for a parameter group and deselect **Enable short query acceleration**\.   As a best practice, we recommend using a WLM query slot count of 15 or fewer to maintain optimum overall system performance\. For information about modifying WLM configurations, see [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) in the *Amazon Redshift Cluster Management Guide*\.

## Maximum runtime for short queries<a name="wlm-sqa-max-run-time"></a>

When you enable SQA, WLM sets the maximum runtime for short queries to dynamic by default\. We recommend keeping the dynamic setting for SQA maximum runtime\. You can override the default setting by specifying a fixed value of 1–20 seconds\.

In some cases, you might consider using different values for the SQA maximum runtime values to improve your system performance\. In such cases, analyze your workload to find the maximum execution time for most of your short\-running queries\. The following query returns the maximum runtime for queries at about the 70th percentile\. 

```
select least(greatest(percentile_cont(0.7) 
within group (order by total_exec_time / 1000000) + 2, 2), 20) 
from stl_wlm_query 
where userid >= 100
and final_state = 'Completed';
```

After you identify a maximum runtime value that works well for your workload, you don't need to change it unless your workload changes significantly\.

## Monitoring SQA<a name="wlm-monitoring-sqa"></a>

To check whether SQA is enabled, run the following query\. If the query returns a row, then SQA is enabled\.

```
select * from stv_wlm_service_class_config 
where service_class = 14;
```

The following query shows the number of queries that went through each query queue \(service class\)\. It also shows the average execution time, the number of queries with wait time at the 90th percentile, and the average wait time\. SQA queries use in service class 14\.

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

To find queries that SQA picked up but that timed out, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class = 14 and a.final_state = 'Evicted' 
order by b.query desc limit 5;
```