# Query priority<a name="query-priority"></a>

Not all queries are of equal importance, and often performance of one workload or set of users might be more important\. If you have enabled [automatic WLM](automatic-wlm.md), you can define the relative importance of queries in a workload by setting a priority value\. The priority is specified for a queue and inherited by all queries associated with the queue\. You associate queries to a queue by mapping user groups and query groups to the queue\. You can set the following priorities \(listed from highest to lowest priority\):

1. `HIGHEST`

1. `HIGH`

1. `NORMAL`

1. `LOW`

1. `LOWEST`

Administrators use these priorities to show the relative importance of their workloads when there are queries with different priorities contending for the same resources\. Amazon Redshift uses the priority when letting queries into the system, and to determine the amount of resources allocated to a query\. By default, queries run with their priority set to `NORMAL`\.

An additional priority, `CRITICAL`, which is a higher priority than `HIGHEST`, is available to superusers\. To set this priority, you can use the functions [CHANGE\_QUERY\_PRIORITY](r_CHANGE_QUERY_PRIORITY.md), [CHANGE\_SESSION\_PRIORITY](r_CHANGE_SESSION_PRIORITY.md)\. and [CHANGE\_USER\_PRIORITY](r_CHANGE_USER_PRIORITY.md)\. To grant a database user permission to use these functions, you can create a stored procedure and grant permission to a user\. For an example, see [CHANGE\_SESSION\_PRIORITY](r_CHANGE_SESSION_PRIORITY.md)\. 

**Note**  
Only one `CRITICAL` query can run at a time\.

Let's take an example where the priority of an extract, transform, load \(ETL\) workload is higher than the priority of the analytics workload\. The ETL workload runs every six hours, and the analytics workload runs throughout the day\. When only the analytics workload is running on the cluster, it gets the entire system to itself, yielding high throughput with optimal system utilization\. However, when the ETL workload starts, it gets the right of the way because it has a higher priority\. Queries running as part of the ETL workload get the right of the way during admission and also preferential resource allocation after they are admitted\. As a consequence, the ETL workload performs predictably regardless of what else might be running on the system\. Thus, it provides predictable performance and the ability for administrators to provide service level agreements \(SLAs\) for their business users\. 

Within a given cluster, the predictable performance for a high priority workload comes at the cost of other, lower priority workloads\. Lower priority workloads might run longer either because their queries are waiting behind more important queries to complete\. Or they might run longer because they're getting a smaller fraction of resources when they are running concurrently with higher priority queries\. Lower priority queries don't suffer from starvation, but rather keep making progress at a slower pace\.

In the preceding example, the administrator can enable [concurrency scaling](concurrency-scaling.md) for the analytics workload\. Doing this enables that workload to maintain its throughput, even though the ETL workload is running at high priority\. 

## Configuring queue priority<a name="concurrency-scaling-queues"></a>

If you have enabled automatic WLM, each queue has a priority value\. Queries are routed to queues based on user groups and query groups\. The default queue priority is `NORMAL`\. Set the priority higher or lower based on the workload associated with the queue's user groups and query groups\. 

You can change the priority of a queue on the Amazon Redshift console\. On the Amazon Redshift console, the **Workload Management** page displays the queues and enables editing of queue properties such as **Priority**\. To set the priority using the CLI or API operations, use the `wlm_json_configuration` parameter\. For more information, see [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) in the *Amazon Redshift Cluster Management Guide*\.

The following `wlm_json_configuration` example defines three user groups \(`ingest`, `reporting`, and `analytics`\)\. Queries submitted from users from one of these groups run with priority `highest`, `normal`, and `low`, respectively\.

```
[
    {
        "user_group": [
            "ingest"
        ],
        "priority": "highest",
        "queue_type": "auto"
    },
    {
        "user_group": [
            "reporting"
        ],
        "priority": "normal",
        "queue_type": "auto"
    },
    {
        "user_group": [
            "analytics"
        ],
        "priority": "low",
        "queue_type": "auto",
        "auto_wlm": true
    }
]
```

## Changing query priority with query monitoring rules<a name="query-priority-qmr"></a>

Query monitoring rules \(QMR\) enable you to change the priority of a query based on its behavior while it is running\. You do this by specifying the priority attribute in a QMR predicate in addition to an action\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\. 

For example, you can define a rule to abort any query classified as `high` priority that runs for more than 10 minutes\.

```
"rules" :[
  {
    "rule_name":"rule_abort",
    "predicate":[
      {
        "metric_name":"query_cpu_time",
        "operator":">",
        "value":600
      },
      {
        "metric_name":"query_priority",
        "operator":"=",
        "value":"high"
      }
    ],
    "action":"abort"
  }
]
```

Another example is to define a rule to change the query priority to `lowest` for any query with current priority `normal` that spills more than 1 TB to disk\. 

```
"rules":[
  {
    "rule_name":"rule_change_priority",
    "predicate":[
      {
        "metric_name":"query_temp_blocks_to_disk",
        "operator":">",
        "value":1000000
      },
      {
        "metric_name":"query_priority",
        "operator":"=",
        "value":"normal"
      }
    ],
    "action":"change_query_priority",
    "value":"lowest"
  }
]
```

## Monitoring query priority<a name="query-priority-monitoring"></a>

To display priority for waiting and running queries, view the `query_priority` column in the stv\_wlm\_query\_state system table\.

```
query    | service_cl | wlm_start_time             | state            | queue_time | query_priority
---------+------------+----------------------------+------------------+------------+----------------
2673299  | 102        | 2019-06-24 17:35:38.866356 | QueuedWaiting    | 265116     | Highest
2673236  | 101        | 2019-06-24 17:35:33.313854 | Running          | 0          | Highest
2673265  | 102        | 2019-06-24 17:35:33.523332 | Running          | 0          | High
2673284  | 102        | 2019-06-24 17:35:38.477366 | Running          | 0          | Highest
2673288  | 102        | 2019-06-24 17:35:38.621819 | Running          | 0          | Highest
2673310  | 103        | 2019-06-24 17:35:39.068513 | QueuedWaiting    | 62970      | High
2673303  | 102        | 2019-06-24 17:35:38.968921 | QueuedWaiting    | 162560     | Normal
2673306  | 104        | 2019-06-24 17:35:39.002733 | QueuedWaiting    | 128691     | Lowest
```

To list query priority for completed queries, see the `query_priority` column in the stl\_wlm\_query system table\.

```
select query, service_class as svclass, service_class_start_time as starttime, query_priority 
from stl_wlm_query order by 3 desc limit 10;
```

```
  query  | svclass |         starttime          |    query_priority
---------+---------+----------------------------+----------------------
 2723254 |     100 | 2019-06-24 18:14:50.780094 | Normal
 2723251 |     102 | 2019-06-24 18:14:50.749961 | Highest  
 2723246 |     102 | 2019-06-24 18:14:50.725275 | Highest
 2723244 |     103 | 2019-06-24 18:14:50.719241 | High
 2723243 |     101 | 2019-06-24 18:14:50.699325 | Low
 2723242 |     102 | 2019-06-24 18:14:50.692573 | Highest
 2723239 |     101 | 2019-06-24 18:14:50.668535 | Low
 2723237 |     102 | 2019-06-24 18:14:50.661918 | Highest
 2723236 |     102 | 2019-06-24 18:14:50.643636 | Highest
```