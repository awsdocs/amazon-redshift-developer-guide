# Creating materialized views in Amazon Redshift<a name="materialized-view-overview"></a>

In a data warehouse environment, applications often need to perform complex queries on large tables—for example, SELECT statements that perform multiple\-table joins and aggregations on tables that contain billions of rows\. Processing these queries can be expensive, in terms of system resources and the time it takes to compute the results\.

Materialized views in Amazon Redshift provide a way to address these issues\. A *materialized view* contains a precomputed result set, based on an SQL query over one or more base tables\. You can issue SELECT statements to query a materialized view, in the same way that you can query other tables or views in the database\. Amazon Redshift returns the precomputed results from the materialized view, without having to access the base tables at all\. From the user standpoint, the query results are returned much faster compared to when retrieving the same data from the base tables\.

Materialized views are especially useful for speeding up queries that are predictable and repeated\. Instead of performing resource\-intensive queries against large database tables \(such as aggregates or multiple\-table joins\), applications can query a materialized view and retrieve a precomputed result set\. For example, consider the scenario where a set of queries is used to populate a collection of charts, such as Amazon QuickSight\.  This use case is ideal for a materialized view, because the queries are predictable and repeated over and over again\. 

When you create a materialized view, Amazon Redshift runs the user\-specified SQL statement to gather the data from the base table or tables and stores the result set\. The following illustration provides an overview of the materialized view `tickets_mv` that an SQL query defines using two base tables `events` and `sales`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/materialized-view.png)

For information on how to create materialized views, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\.

You can issue SELECT statements to query a materialized view\. For information on how to query materialized views, see [Querying a materialized view](#materialized-view-query)\.

The result set eventually becomes stale when data is inserted, updated, and deleted in the base tables\. You can refresh the materialized view at any time to update it with the latest changes from the base tables\. For information on how to refresh materialized views, see [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)\.

For details about SQL commands used to create and manage materialized views, see the following command topics:
+ [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)
+ [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)
+ [DROP MATERIALIZED VIEW](materialized-view-drop-sql-command.md)

For information about system tables and views to monitor materialized views, see the following topics: 
+ [STV\_MV\_INFO](r_STV_MV_INFO.md)
+ [STL\_MV\_STATE](r_STL_MV_STATE.md)
+ [SVL\_MV\_REFRESH\_STATUS](r_SVL_MV_REFRESH_STATUS.md)

## Querying a materialized view<a name="materialized-view-query"></a>

You can use a materialized view in any SQL query by referencing the materialized view name as the data source, like a table or standard view\.

When a query accesses a materialized view, it sees only the data that is stored in the materialized view as of its most recent refresh\. Thus, the query might not see all the latest changes from corresponding base tables of the materialized view\.

**Note**  
If other users want to query the materialized view, the owner of the materialized view grants the SELECT privilege to those users\. The other users don't need to have the SELECT privilege on the underlying base tables\.
The owner of the materialized view can also revoke the SELECT privilege from other users, to prevent them from querying the materialized view\.
If the owner of the materialized view no longer has the SELECT privilege on the underlying base tables\.  
The owner can no longer query the materialized view\. 
Other users who have the SELECT privilege on the materialized view can no longer query the materialized view\.

The following example queries the `tickets_mv` materialized view\. For more information on the SQL command used to create a materialized view, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\.

```
SELECT sold
FROM tickets_mv
WHERE catgroup = 'Concerts';
```

Because the query results are precomputed, there's no need to access the underlying tables \(`category`, `event`, and `sales`\)\. Amazon Redshift can return the results directly from `tickets_mv`\.