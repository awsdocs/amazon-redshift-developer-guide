# WLM query queue hopping<a name="wlm-queue-hopping"></a>

A query can be hopped due to a [WLM timeout](cm-c-defining-query-queues.md#wlm-timeout) or a [query monitoring rule \(QMR\) hop action](cm-c-wlm-query-monitoring-rules.md#cm-c-wlm-defining-query-monitoring-rules)\. You can only hop queries in a manual WLM configuration\. 

When a query is hopped, WLM attempts to route the query to the next matching queue based on the [WLM queue assignment rules](cm-c-wlm-queue-assignment-rules.md)\. If the query doesn't match any other queue definition, the query is canceled\. It's not assigned to the default queue\. 

## WLM timeout actions<a name="wlm-queue-hopping-summary"></a>

The following table summarizes the behavior of different types of queries with a WLM timeout\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/wlm-queue-hopping.html)

## WLM timeout queue hopping<a name="wlm-timeout-queue-hopping"></a>

WLM hops the following types of queries when they time out:
+ Read\-only queries, such as SELECT statements, that are in a WLM state of `running`\. To find the WLM state of a query, view the STATE column on the [STV\_WLM\_QUERY\_STATE](r_STV_WLM_QUERY_STATE.md) system table\. 
+ CREATE TABLE AS \(CTAS\) statements\. WLM queue hopping supports both user\-defined and system\-generated CTAS statements\. 
+ SELECT INTO statements\.

Queries that aren't subject to WLM timeout continue running in the original queue until completion\. The following types of queries aren't subject to WLM timeout:
+ COPY statements
+ Maintenance operations, such as ANALYZE and VACUUM
+ Read\-only queries, such as SELECT statements, that have reached a WLM state of `returning`\. To find the WLM state of a query, view the STATE column on the [STV\_WLM\_QUERY\_STATE](r_STV_WLM_QUERY_STATE.md) system table\. 

Queries that aren't eligible for hopping by WLM timeout are canceled when they time out\. The following types of queries are not eligible for hopping by a WLM timeout:
+ INSERT, UPDATE, and DELETE statements
+ UNLOAD statements
+ User\-defined functions \(UDFs\)

## WLM timeout reassigned and restarted queries<a name="wlm-timeout-reassigned-and-restarted-queries"></a>

When a query is hopped and no matching queue is found, the query is canceled\.

When a query is hopped and a matching queue is found, WLM attempts to reassign the query to the new queue\. If a query can't be reassigned, it's restarted in the new queue, as described following\.

A query is reassigned only if all of the following are true:
+ A matching queue is found\.
+ The new queue has enough free slots to run the query\. A query might require multiple slots if the [wlm\_query\_slot\_count](r_wlm_query_slot_count.md) parameter was set to a value greater than 1\.
+ The new queue has at least as much memory available as the query currently uses\. 

If the query is reassigned, the query continues executing in the new queue\. Intermediate results are preserved, so there is minimal effect on total execution time\. 

If the query can't be reassigned, the query is canceled and restarted in the new queue\. Intermediate results are deleted\. The query waits in the queue, then begins running when enough slots are available\.

## QMR hop actions<a name="qmr-hop-action-queue-hopping"></a>

The following table summarizes the behavior of different types of queries with a QMR hop action\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/wlm-queue-hopping.html)

To find whether a query that was hopped by QMR was reassigned, restarted, or canceled, query the [STL\_WLM\_RULE\_ACTION](r_STL_WLM_RULE_ACTION.md) system log table\.

## QMR hop action reassigned and restarted queries<a name="qmr-hop-action-reassigned-and-restarted-queries"></a>

When a query is hopped and no matching queue is found, the query is canceled\.

When a query is hopped and a matching queue is found, WLM attempts to reassign the query to the new queue\. If a query can't be reassigned, it's restarted in the new queue or continues execution in the original queue, as described following\.

A query is reassigned only if all of the following are true:
+ A matching queue is found\.
+ The new queue has enough free slots to run the query\. A query might require multiple slots if the [wlm\_query\_slot\_count](r_wlm_query_slot_count.md) parameter was set to a value greater than 1\.
+ The new queue has at least as much memory available as the query currently uses\. 

If the query is reassigned, the query continues executing in the new queue\. Intermediate results are preserved, so there is minimal effect on total execution time\. 

If a query can't be reassigned, the query is either restarted or continues execution in the original queue\. If the query is restarted, the query is canceled and restarted in the new queue\. Intermediate results are deleted\. The query waits in the queue, then begins execution when enough slots are available\.