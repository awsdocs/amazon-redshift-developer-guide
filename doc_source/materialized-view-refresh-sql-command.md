# REFRESH MATERIALIZED VIEW<a name="materialized-view-refresh-sql-command"></a>

Refreshes a materialized view\.

When you create a materialized view, its contents reflect the state of the underlying database table or tables at that time\. The data in the materialized view remains unchanged, even when applications make changes to the data in the underlying tables\. To update the data in the materialized view, you can use the `REFRESH MATERIALIZED VIEW` statement at any time\. When you use this statement, Amazon Redshift identifies changes that have taken place in the base table or tables, and then applies those changes to the materialized view\.

`REFRESH MATERIALIZED VIEW` attempts to perform an incremental refresh\. However, if the underlying SQL query for the materialized view can't be incrementally refreshed, then Amazon Redshift refreshes by full recomputation of the materialized view\. For information about which SQL elements can't support incremental refreshes, see [Limitations and Usage Notes for Materialized Views](materialized-view-usage-notes.md)\. 

For more information about materialized views, see [Creating Materialized Views in Amazon Redshift](materialized-view-overview.md)\.

## Syntax<a name="mv_REFRESH_MATERIALIZED_VIEW-synopsis"></a>

```
REFRESH MATERIALIZED VIEW mv_name
```

## Parameters<a name="mv_REFRESH_MATERIALIZED_VIEW-parameters"></a>

*mv\_name*  
The name of the materialized view to be refreshed\.

## Usage Notes<a name="mv_REFRESH_MARTERIALIZED_VIEW_usage"></a>

Only the owner of a materialized view can perform a `REFRESH MATERIALIZED VIEW` operation on that view\. If the owner no longer has SELECT privilege on the underlying base tables, `REFRESH MATERIALIZED VIEW` doesn't run\. 

For incremental materialized views, `REFRESH MATERIALIZED VIEW` uses only those base table rows that are already committed\. Therefore, if the refresh operation runs after a data manipulation language \(DML\) statement in the same transaction, then changes of that DML statement aren't visible to refresh\. 

Furthermore, take a case where a transaction B follows a transaction A\. In such a case, `REFRESH MATERIALIZED VIEW` doesn't see some committed base table rows that are updated by transaction B while the older transaction A is in progress\. These omitted rows are updated by subsequent refresh operations\.

For a full refresh of a materialized view, `REFRESH MATERIALIZED VIEW` sees all base table rows visible to the refresh transaction, according to usual Amazon Redshift transaction semantics\. 

Some operations in Amazon Redshift interact with materialized views\. Some of these operations might force a `REFRESH MATERIALIZED VIEW` operation to fully recompute the materialized view\. An operation can force a full refresh even when the materialized view only uses the SQL features that lend themselves to incremental refresh\. For more information, see [Limitations and Usage Notes for Materialized Views](materialized-view-usage-notes.md)\. 

## Examples<a name="mv_REFRESH_MARTERIALIZED_VIEW_examples"></a>

The following example refreshes the `tickets_mv` materialized view\.

```
REFRESH MATERIALIZED VIEW tickets_mv;
```