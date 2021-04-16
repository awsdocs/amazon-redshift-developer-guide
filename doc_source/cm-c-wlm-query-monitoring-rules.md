# WLM query monitoring rules<a name="cm-c-wlm-query-monitoring-rules"></a>

In Amazon Redshift workload management \(WLM\), query monitoring rules define metrics\-based performance boundaries for WLM queues and specify what action to take when a query goes beyond those boundaries\. For example, for a queue dedicated to short running queries, you might create a rule that aborts queries that run for more than 60 seconds\. To track poorly designed queries, you might have another rule that logs queries that contain nested loops\. 

You define query monitoring rules as part of your workload management \(WLM\) configuration\. You can define up to 25 rules for each queue, with a limit of 25 rules for all queues\. Each rule includes up to three conditions, or predicates, and one action\. A *predicate* consists of a metric, a comparison condition \(=, <, or > \), and a value\. If all of the predicates for any rule are met, that rule's action is triggered\. Possible rule actions are log, hop, and abort, as discussed following\. 

The rules in a given queue apply only to queries running in that queue\. A rule is independent of other rules\. 

WLM evaluates metrics every 10 seconds\. If more than one rule is triggered during the same period, WLM initiates the most severe action—abort, then hop, then log\. If the action is hop or abort, the action is logged and the query is evicted from the queue\. If the action is log, the query continues to run in the queue\. WLM initiates only one log action per query per rule\. If the queue contains other rules, those rules remain in effect\. If the action is hop and the query is routed to another queue, the rules for the new queue apply\. 

When all of a rule's predicates are met, WLM writes a row to the [STL\_WLM\_RULE\_ACTION](r_STL_WLM_RULE_ACTION.md) system table\. In addition, Amazon Redshift records query metrics for currently running queries to [STV\_QUERY\_METRICS](r_STV_QUERY_METRICS.md)\. Metrics for completed queries are stored in [STL\_QUERY\_METRICS](r_STL_QUERY_METRICS.md)\. 

## Defining a query monitoring rule<a name="cm-c-wlm-defining-query-monitoring-rules"></a>

You create query monitoring rules as part of your WLM configuration, which you define as part of your cluster's parameter group definition\.

You can create rules using the AWS Management Console or programmatically using JSON\. 

**Note**  
If you choose to create rules programmatically, we strongly recommend using the console to generate the JSON that you include in the parameter group definition\. For more information, see [Creating or Modifying a Query Monitoring Rule Using the Console](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html#parameter-group-modify-qmr-console) and [Configuring Parameter Values Using the AWS CLI](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html#configure-parameters-using-the-cli) in the *Amazon Redshift Cluster Management Guide*\. 

To define a query monitoring rule, you specify the following elements:
+ A rule name – Rule names must be unique within the WLM configuration\. Rule names can be up to 32 alphanumeric characters or underscores, and can't contain spaces or quotation marks\. You can have up to 25 rules per queue, and the total limit for all queues is 25 rules\.
+ One or more predicates – You can have up to three predicates per rule\. If all the predicates for any rule are met, the associated action is triggered\. A predicate is defined by a metric name, an operator \( =, <, or > \), and a value\. An example is `query_cpu_time > 100000`\. For a list of metrics and examples of values for different metrics, see [Query monitoring metrics](#cm-c-wlm-query-monitoring-metrics) following in this section\. 
+ An action – If more than one rule is triggered, WLM chooses the rule with the most severe action\. Possible actions, in ascending order of severity, are:
  + Log – Record information about the query in the STL\_WLM\_RULE\_ACTION system table\. Use the Log action when you want to only write a log record\. WLM creates at most one log per query, per rule\. Following a log action, other rules remain in force and WLM continues to monitor the query\. 
  + Hop \(only available with manual WLM\) – Log the action and hop the query to the next matching queue\. If there isn't another matching queue, the query is canceled\. QMR hops only [CREATE TABLE AS](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_TABLE_AS.html) \(CTAS\) statements and read\-only queries, such as SELECT statements\. For more information, see [WLM query queue hopping](wlm-queue-hopping.md)\. 
  + Abort – Log the action and terminate the query\. QMR doesn't abort COPY statements and maintenance operations, such as ANALYZE and VACUUM\. 
  + Change priority \(only available with automatic WLM\) – Change the priority of a query\. 

To limit the runtime of queries, we recommend creating a query monitoring rule instead of using WLM timeout\. For example, you can set `max_execution_time` to 50,000 milliseconds as shown in the following JSON snippet\.

```
"max_execution_time": 50000
```

But we recommend instead that you define an equivalent query monitoring rule that sets `query_execution_time` to 50 seconds as shown in the following JSON snippet\.

```
"rules": 
[
    {
        "rule_name": "rule_query_execution",
        "predicate": [
            {
                "metric_name": "query_execution_time",
                "operator": ">",
                "value": 50
            }
        ],
        "action": "abort"
    }            
]
```

For steps to create or modify a query monitoring rule, see [Creating or Modifying a Query Monitoring Rule Using the Console](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html#parameter-group-modify-qmr-console) and [Properties in the wlm\_json\_configuration Parameter](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html#wlm-json-config-properties) in the *Amazon Redshift Cluster Management Guide*\.

You can find more information about query monitoring rules in the following topics: 
+  [Query monitoring metrics](#cm-c-wlm-query-monitoring-metrics) 
+  [Query monitoring rules templates](#cm-c-wlm-query-monitoring-templates) 
+  [Creating a Rule Using the Console](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-parameter-groups-console.html#parameter-group-modify-qmr-console) 
+  [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) 
+  [System tables and views for query monitoring rules](#cm-c-wlm-qmr-tables-and-views) 

## Query monitoring metrics<a name="cm-c-wlm-query-monitoring-metrics"></a>

The following table describes the metrics used in query monitoring rules\. \(These metrics are distinct from the metrics stored in the [STV\_QUERY\_METRICS](r_STV_QUERY_METRICS.md) and [STL\_QUERY\_METRICS](r_STL_QUERY_METRICS.md) system tables\.\) 

For a given metric, the performance threshold is tracked either at the query level or the segment level\. For more information about segments and steps, see [Query planning and execution workflow](c-query-planning.md)\.

**Note**  
The [WLM timeout](cm-c-defining-query-queues.md#wlm-timeout) parameter is distinct from query monitoring rules\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-query-monitoring-rules.html)

**Note**  
The hop action is not supported with the `query_queue_time` predicate\. That is, rules defined to hop when a `query_queue_time` predicate is met are ignored\. 
Short segment execution times can result in sampling errors with some metrics, such as `io_skew` and `query_cpu_percent`\. To avoid or reduce sampling errors, include segment execution time in your rules\. A good starting point is `segment_execution_time > 10`\.

The [SVL\_QUERY\_METRICS](r_SVL_QUERY_METRICS.md) view shows the metrics for completed queries\. The [SVL\_QUERY\_METRICS\_SUMMARY](r_SVL_QUERY_METRICS_SUMMARY.md) view shows the maximum values of metrics for completed queries\. Use the values in these views as an aid to determine threshold values for defining query monitoring rules\.

## Query monitoring rules templates<a name="cm-c-wlm-query-monitoring-templates"></a>

When you add a rule using the Amazon Redshift console, you can choose to create a rule from a predefined template\. Amazon Redshift creates a new rule with a set of predicates and populates the predicates with default values\. The default action is log\. You can modify the predicates and action to meet your use case\. 

The following table lists available templates\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-query-monitoring-rules.html)

## System tables and views for query monitoring rules<a name="cm-c-wlm-qmr-tables-and-views"></a>

When all of a rule's predicates are met, WLM writes a row to the [STL\_WLM\_RULE\_ACTION](r_STL_WLM_RULE_ACTION.md) system table\. This row contains details for the query that triggered the rule and the resulting action\. 

In addition, Amazon Redshift records query metrics the following system tables and views\.
+ The [STV\_QUERY\_METRICS](r_STV_QUERY_METRICS.md) table displays the metrics for currently running queries\.
+ The [STL\_QUERY\_METRICS](r_STL_QUERY_METRICS.md) table records the metrics for completed queries\. 
+ The [SVL\_QUERY\_METRICS](r_SVL_QUERY_METRICS.md) view shows the metrics for completed queries\. 
+ The [SVL\_QUERY\_METRICS\_SUMMARY](r_SVL_QUERY_METRICS_SUMMARY.md) view shows the maximum values of metrics for completed queries\.