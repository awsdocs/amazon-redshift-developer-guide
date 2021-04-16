# Implementing automatic WLM<a name="automatic-wlm"></a>

With automatic workload management \(WLM\), Amazon Redshift manages query concurrency and memory allocation\. Up to eight queues are created with the service class identifiers 100–107\. Each queue has a priority\. For more information, see [Query priority](query-priority.md)\. 

In contrast, manual WLM requires you to specify values for query concurrency and memory allocation\. The default for manual WLM is concurrency of five queries, and memory is divided equally between all five\. Automatic WLM determines the amount of resources that queries need, and adjusts the concurrency based on the workload\. When queries requiring large amounts of resources are in the system \(for example, hash joins between large tables\), the concurrency is lower\. When lighter queries \(such as inserts, deletes, scans, or simple aggregations\) are submitted, concurrency is higher\. 

For details about how to migrate from manual WLM to automatic WLM, see [Migrating from manual WLM to automatic WLM](cm-c-modifying-wlm-configuration.md#wlm-manual-to-automatic)\.

Automatic WLM is separate from short query acceleration \(SQA\) and it evaluates queries differently\. Automatic WLM and SQA work together to allow short running and lightweight queries to complete even while long running, resource intensive queries are active\. For more information about SQA, see [Working with short query acceleration](wlm-short-query-acceleration.md)\. 

Amazon Redshift enables automatic WLM through parameter groups:
+ If your clusters use the default parameter group, Amazon Redshift enables automatic WLM for them\.
+ If your clusters use custom parameter groups, you can configure the clusters to enable automatic WLM\. We recommend that you create a separate parameter group for your automatic WLM configuration\. 

To configure WLM, edit the `wlm_json_configuration` parameter in a parameter group that can be associated with one or more clusters\. For more information, see [Modifying the WLM configuration](cm-c-modifying-wlm-configuration.md)\.

You define query queues within the WLM configuration\. You can add additional query queues to the default WLM configuration, up to a total of eight user queues\. You can configure the following for each query queue: 
+ Priority 
+ Concurrency scaling mode 
+ User groups 
+ Query groups 
+ Query monitoring rules 

## Priority<a name="wlm-auto-query-priority"></a>

You can define the relative importance of queries in a workload by setting a priority value\. The priority is specified for a queue and inherited by all queries associated with the queue\. For more information, see [Query priority](query-priority.md)\.

## Concurrency scaling mode<a name="wlm-auto-concurrency-scaling-mode"></a>

When concurrency scaling is enabled, Amazon Redshift automatically adds additional cluster capacity when you need it to process an increase in concurrent read queries\. Write operations continue as normal on your main cluster\. Users see the most current data, whether the queries run on the main cluster or on a concurrency scaling cluster\. 

You manage which queries are sent to the concurrency scaling cluster by configuring WLM queues\. When you enable concurrency scaling for a queue, eligible queries are sent to the concurrency scaling cluster instead of waiting in line\. For more information, see [Working with concurrency scaling](concurrency-scaling.md)\.

## User groups<a name="wlm-auto-defining-query-queues-user-groups"></a>

You can assign a set of user groups to a queue by specifying each user group name or by using wildcards\. When a member of a listed user group runs a query, that query runs in the corresponding queue\. There is no set limit on the number of user groups that can be assigned to a queue\. For more information, see [Wildcards](#wlm-auto-wildcards)\.

## Query groups<a name="wlm-auto-defining-query-queues-query-groups"></a>

You can assign a set of query groups to a queue by specifying each query group name or by using wildcards\. A *query group* is simply a label\. At runtime, you can assign the query group label to a series of queries\. Any queries that are assigned to a listed query group run in the corresponding queue\. There is no set limit to the number of query groups that can be assigned to a queue\. For more information, see [Wildcards](#wlm-auto-wildcards)\.

## Wildcards<a name="wlm-auto-wildcards"></a>

If wildcards are enabled in the WLM queue configuration, you can assign user groups and query groups to a queue either individually or by using Unix shell–style wildcards\. The pattern matching is case\-insensitive\. 

For example, the '\*' wildcard character matches any number of characters\. Thus, if you add `dba_*` to the list of user groups for a queue, any user\-run query that belongs to a group with a name that begins with `dba_` is assigned to that queue\. Examples are `dba_admin` or `DBA_primary`\. The '?' wildcard character matches any single character\. Thus, if the queue includes user\-group `dba?1`, then user groups named `dba11` and `dba21` match, but `dba12` doesn't match\. 

By default, wildcards aren't enabled\.

## Query monitoring rules<a name="wlm-auto-query-monitoring-rules"></a>

Query monitoring rules define metrics\-based performance boundaries for WLM queues and specify what action to take when a query goes beyond those boundaries\. For example, for a queue dedicated to short running queries, you might create a rule that aborts queries that run for more than 60 seconds\. To track poorly designed queries, you might have another rule that logs queries that contain nested loops\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.

## Checking for automatic WLM<a name="wlm-monitoring-automatic-wlm"></a>

To check whether automatic WLM is enabled, run the following query\. If the query returns at least one row, then automatic WLM is enabled\.

```
select * from stv_wlm_service_class_config 
where service_class >= 100;
```

The following query shows the number of queries that went through each query queue \(service class\)\. It also shows the average execution time, the number of queries with wait time at the 90th percentile, and the average wait time\. Automatic WLM queries use service classes 100 to 107\.

```
select final_state, service_class, count(*), avg(total_exec_time), 
percentile_cont(0.9) within group (order by total_queue_time), avg(total_queue_time) 
from stl_wlm_query where userid >= 100 group by 1,2 order by 2,1;
```

To find which queries were run by automatic WLM, and completed successfully, run the following query\.

```
select a.queue_start_time, a.total_exec_time, label, trim(querytxt) 
from stl_wlm_query a, stl_query b 
where a.query = b.query and a.service_class >= 100 and a.final_state = 'Completed' 
order by b.query desc limit 5;
```