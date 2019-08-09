# Automatic Workload Management \(WLM\)<a name="automatic-wlm"></a>

With automatic workload management \(WLM\), Amazon Redshift manages query concurrency and memory allocation\. One query queue is created with the service class ID 100\. 

In contrast, manual WLM requires you to specify values for query concurrency and memory allocation\. The default for manual WLM is concurrency of five queries, and memory is divided equally between all five\. Automatic workload management determines the amount of resources that queries need, and adjusts the concurrency based on the workload\. When queries requiring large amounts of resources are in the system \(for example, hash joins between large tables\), the concurrency is lower\. When lighter queries \(such as inserts, deletes, scans, or simple aggregations\) are submitted, concurrency is higher\. 

**Note**  
Automatic WLM is enabled in the following ways:  
Amazon Redshift enables automatic WLM on clusters using the default parameter group\.
If you are using custom parameter groups, you can configure them to enable automatic WLM\. For more information, see [Implementing Workload Management](cm-c-implementing-workload-management.md)\.

## Monitoring Automatic WLM<a name="wlm-monitoring-automatic-wlm"></a>

To check whether automatic WLM is enabled, run the following query\. If the query returns a row, then automatic WLM is enabled\.

```
select * from stv_wlm_service_class_config 
where service_class = 100;
```

The following query shows the number of queries that went through each query queue \(service class\)\. It also shows the average execution time, the number of queries with wait time at the 90th percentile, and the average wait time\. Automatic WLM queries use service class 100\.

```
select final_state, service_class, count(*), avg(total_exec_time), 
percentile_cont(0.9) within group (order by total_queue_time), avg(total_queue_time) 
from stl_wlm_query where userid >= 100 group by 1,2 order by 2,1;
```

To find which queries were run by automatic WLM, and completed successfully, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class = 100 and a.final_state = 'Completed' 
order by b.query desc limit 5;
```

To find queries that automatic WLM ran but that timed out, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class = 100 and a.final_state = 'Evicted' 
order by b.query desc limit 5;
```