# Querying a materialized view<a name="materialized-view-query"></a>

You can use a materialized view in any SQL query by referencing the materialized view name as the data source, like a table or standard view\.

When a query accesses a materialized view, it sees only the data that is stored in the materialized view as of its most recent refresh\. Thus, the query might not see all the latest changes from corresponding base tables of the materialized view\.

If other users want to query the materialized view, the owner of the materialized view grants the SELECT privilege to those users\. The other users don't need to have the SELECT privilege on the underlying base tables\. The owner of the materialized view can also revoke the SELECT privilege from other users to prevent them from querying the materialized view\.

If the owner of the materialized view no longer has the SELECT privilege on the underlying base tables:
+ The owner can no longer query the materialized view\. 
+ Other users who have the SELECT privilege on the materialized view can no longer query the materialized view\.

The following example queries the `tickets_mv` materialized view\. For more information on the SQL command used to create a materialized view, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\.

```
SELECT sold
FROM tickets_mv
WHERE catgroup = 'Concerts';
```

Because the query results are precomputed, there's no need to access the underlying tables \(`category`, `event`, and `sales`\)\. Amazon Redshift can return the results directly from `tickets_mv`\.