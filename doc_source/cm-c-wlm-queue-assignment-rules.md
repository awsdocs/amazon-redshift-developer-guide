# WLM queue assignment rules<a name="cm-c-wlm-queue-assignment-rules"></a>

When a user runs a query, WLM assigns the query to the first matching queue, based on the WLM queue assignment rules:

1. If a user is logged in as a superuser and runs a query in the query group labeled superuser, the query is assigned to the superuser queue\.

1. If a user belongs to a listed user group or runs a query within a listed query group, the query is assigned to the first matching queue\.

1. If a query doesn't meet any criteria, the query is assigned to the default queue, which is the last queue defined in the WLM configuration\.

The following diagram illustrates how these rules work\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/queue-assignment-rules-2.png)

## Queue assignments example<a name="cm-c-wlm-queue-assignment-rules-queue-assignments-example"></a>

The following table shows a WLM configuration with the superuser queue and four user\-defined queues\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/workflow-queues.png)

The following illustration shows how queries are assigned to the queues in the previous table according to user groups and query groups\. For information about how to assign queries to user groups and query groups at runtime, see [Assigning queries to queues](cm-c-executing-queries.md) later in this section\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/queues-assignment.png)

In this example, WLM makes the following assignments:

1. The first set of statements shows three ways to assign users to user groups\. The statements are executed by the user `masteruser`, which is not a member of a user group listed in any WLM queue\. No query group is set, so the statements are routed to the default queue\.

1. The user `masteruser` is a superuser and the query group is set to `'superuser'`, so the query is assigned to the superuser queue\.

1. The user `admin1` is a member of the user group listed in queue 1, so the query is assigned to queue 1\.

1. The user `vp1` is not a member of any listed user group\. The query group is set to `'QG_B'`, so the query is assigned to queue 2\. 

1. The user `analyst1` is a member of the user group listed in queue 3, but `'QG_B'` matches queue 2, so the query is assigned to queue 2\. 

1. The user `ralph` is not a member of any listed user group and the query group was reset, so there is no matching queue\. The query is assigned to the default queue\. 