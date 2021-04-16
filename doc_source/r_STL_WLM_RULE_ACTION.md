# STL\_WLM\_RULE\_ACTION<a name="r_STL_WLM_RULE_ACTION"></a>

Records details about actions resulting from WLM query monitoring rules associated with user\-defined queues\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_WLM_RULE_ACTION-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_WLM_RULE_ACTION.html)

## Sample queries<a name="r_STL_WLM_RULE_ACTION-sample-queries"></a>

The following example finds queries that were aborted by a query monitoring rule\.

```
Select query, rule
from stl_wlm_rule_action 
where action = 'abort'
order by query;
```