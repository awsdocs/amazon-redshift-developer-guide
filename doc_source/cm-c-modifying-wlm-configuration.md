# Modifying the WLM configuration<a name="cm-c-modifying-wlm-configuration"></a>

The easiest way to modify the WLM configuration is by using the Amazon Redshift console\. You can also use the AWS CLI or the Amazon Redshift API\. 

When you switch your cluster between automatic and manual WLM, your cluster is put into `pending reboot` state\. The change doesn't take effect until the next cluster reboot\.

For detailed information about modifying WLM configurations, see [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html) in the *Amazon Redshift Cluster Management Guide\.*

## Migrating from manual WLM to automatic WLM<a name="wlm-manual-to-automatic"></a>

To maximize system throughput and use resources most effectively, we recommend that you set up automatic WLM for your queues\. Consider taking the following approach to set up a smooth transition from manual WLM to automatic WLM\.

To migrate from manual WLM to automatic WLM and use query priorities, we recommend that you create a new parameter group and then attach that parameter group to your cluster\. For more information, see [Amazon Redshift Parameter Groups](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html) in the *Amazon Redshift Cluster Management Guide\.*\. 

**Important**  
To change the parameter group or to switch from manual to automatic WLM requires a cluster reboot\. For more information, see [WLM dynamic and static configuration properties](cm-c-wlm-dynamic-properties.md)\.

Let's take an example where there are three manual WLM queues\. One each for an ETL workload, an analytics workload, and a data science workload\. The ETL workload runs every 6 hours, the analytics workload runs throughout the day, and the data science workload can spike at any time\. With manual WLM, you specify the memory and concurrency that each workload queue gets based on your understanding of the importance of each workload to the business\. Specifying the memory and concurrency is not just hard to figure out, but it also results in cluster resources being statically partitioned and thereby wasted when only a subset of the workloads is running\.

You can use automatic WLM with query priorities to indicate the relative priorities of the workloads, avoiding the preceding issues\. For this example, follow these steps:
+ Create a new parameter group and switch to **Auto WLM** mode\.
+ Add queues for each of the three workloads: ETL workload, analytics workload, and data science workload\. Use the same user groups for each workload that was used with **Manual WLM** mode\. 
+ Set the priority for the ETL workload to `High`, the analytics workload to `Normal`, and the data science to `Low`\. These priorities reflect your business priorities for the different workloads or user groups\. 
+ Optionally, enable concurrency scaling for the analytics or data science queue so that queries in these queues get consistent performance even when the ETL workload is executing every 6 hours\.

With query priorities, when only the analytics workload is running on the cluster, it gets the entire system to itself yielding high throughput with optimal system utilization\. However, when the ETL workload starts, it gets the right of the way since it has a higher priority\. Queries running as part of the ETL workload get priority during admission in addition to preferential resource allocation after they are admitted\. As a consequence, the ETL workload performs predictably regardless of what else might be running on the system\. The predictable performance for a high priority workload comes at the cost of other, lower priority workloads that run longer either because their queries are waiting behind more important queries to complete\. Or, because they are getting a smaller fraction of resources when they are running concurrently with higher priority queries\. The scheduling algorithms used by Amazon Redshift ensure that the lower priority queries do not suffer from starvation, but rather continue to make progress albeit at a slower pace\.

**Note**  
The timeout field is not available in automatic WLM\. Instead, use the QMR rule, `query_execution_time`\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.
The QMR action, HOP, is not applicable to automatic WLM\. Instead, use the `change priority` action\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.
Within a parameter group, avoid mixing automatic WLM queues and manual WLM queues\. Instead, create a new parameter group when migrating to automatic WLM\. 