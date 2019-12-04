# REFRESH MATERIALIZED VIEW<a name="mv-refresh"></a>


|  | 
| --- |
| This is prerelease documentation for the materialized view feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Refreshes a materialized view\.

When you create a materialized view, its contents reflect the state of the underlying database table or tables at that time\. The data in the materialized view remains unchanged, even when applications make changes to the data in the underlying tables\. To update the data in the materialized view, you can use the `REFRESH MATERIALIZED VIEW` statement at any time\. When you do this, Amazon Redshift identifies changes that have taken place in the base table or tables, and then applies those changes to the materialized view\.

`REFRESH MATERIALIZED VIEW` attempts to perform an incremental refresh\. However, if the underlying SQL query for the materialized view cannot be incrementally refreshed, then Amazon Redshift refreshes by full recomputation of the materialized view\. The following SQL elements are compatible with incremental refreshes:
+  `WHERE` 
+  `INNER JOIN` 
+  `GROUP BY` 
+  `HAVING` 
+ Aggregate functions: `COUNT` or `SUM` 
+ Immutable functions that aren't impacted by the environment, or whose output doesn't change for the same input between multiple locations\. For example, functions such as `SYSDATE`, `TIMEOFDAY`, `CURRENT_DATE`, `CURRENT_USER` and `RANDOM` are not immutable\. For more information, see [Limitations and Usage Notes](mv-usage-notes.md)\. 

 If the underlying SQL statement uses any other elements, then Amazon Redshift can't perform an incremental refresh\. Amazon Redshift then needs to perform a full refresh instead\.

## Syntax<a name="mv_REFRESH_MATERIALIZED_VIEW-synopsis"></a>

```
REFRESH MATERIALIZED VIEW mv_name
```

## Parameters<a name="mv_REFRESH_MATERIALIZED_VIEW-parameters"></a>

*mv\_name*  
The name of the materialized view to be refreshed\.

## Usage Notes<a name="mv_REFRESH_MARTERIALIZED_VIEW_usage"></a>

Only the owner of a materialized view can `REFRESH` it\. If the owner no longer has SELECT privilege on the underlying base tables, REFRESH does not run\. 

For incremental materialized views, REFRESH uses only those base table rows that are already committed\. Therefore, if REFRESH runs after a DML in the same transaction, then changes of that DML are not visible to REFRESH\. Furthermore, REFRESH does not see some committed base table rows that are updated by transaction A, while an older transaction B is in progress\. These omitted rows are updated by subsequent REFRESH operations\.

For a full refresh only materialized view, REFRESH sees all base table rows visible to the REFRESH transaction, according to the rules of snapshot isolation\.

Some operations in Amazon Redshift interact with materialized views\. Some of these operations might force a REFRESH to fully recompute the materialized view, even when the materialized view only uses the SQL features that lend themselves to incremental refresh\. For more information, see [Limitations and Usage Notes](mv-usage-notes.md)\. 

## Examples<a name="mv_REFRESH_MARTERIALIZED_VIEW_examples"></a>

The following example refreshes the `tickets_mv` materialized view\.

```
REFRESH MATERIALIZED VIEW tickets_mv;
```