# Creating Materialized Views in Amazon Redshift<a name="materialized-view-overview"></a>

In a data warehouse environment, applications often need to perform complex queries on large tables—for example, SELECT statements that perform multiple\-table joins and aggregations on tables that contain billions of rows\. Processing these queries can be expensive, in terms of system resources and the time it takes to compute the results\.

Materialized views in Amazon Redshift provide a way to address these issues\. A *materialized view* contains a precomputed result set, based on an SQL query over one or more base tables\. You can issue SELECT statements to query a materialized view, in the same way that you can query other tables or views in the database\. Amazon Redshift returns the precomputed results from the materialized view, without having to access the base tables at all\. From the user standpoint, the query results are returned much faster compared to when retrieving the same data from the base tables\.

Materialized views are especially useful for speeding up queries that are predictable and repeated\. Instead of performing resource\-intensive queries against large database tables \(such as aggregates or multiple\-table joins\), applications can query a materialized view and retrieve a precomputed result set\. For example, consider the scenario where a set of queries is used to populate a collection of charts, such as Amazon QuickSight\.  This use case is ideal for a materialized view, because the queries are predictable and repeated over and over again\. 

When you create a materialized view, Amazon Redshift runs the user\-specified SQL statement to gather the data from the base table or tables and store the result set\.  The result set eventually becomes stale, as data is inserted, updated, and deleted in the base table or tables\. To address this issue, you can refresh the materialized view at any time\. Amazon Redshift has two strategies for refreshing a materialized view: 
+ In many cases, Amazon Redshift can perform an incremental refresh\. In an *incremental refresh, *changes to the data in the base tables are quickly identified, and then the data in the materialized view is updated with these changes\.
+ If an incremental refresh isn't possible, then Amazon Redshift performs a full refresh\. A *full refresh* reruns the underlying SQL statement, replacing all of the data in the materialized view\.

To use materialized views during the preview:
+ To use materialized views, you must confirm that your Amazon Redshift cluster is using the cluster maintenance track for **Preview** with the track named **preview\_features**\. The current cluster maintenance version for the preview track is `1.0.12923`\. For more information, see [Choosing Cluster Maintenance Tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks) in the [Amazon Redshift Cluster Management Guide](https://docs.aws.amazon.com/redshift/latest/mgmt/)\.
+ Create a cluster to work with the preview\. For more information, see [Creating a Cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the [Amazon Redshift Cluster Management Guide](https://docs.aws.amazon.com/redshift/latest/mgmt/)\.
+ The materialized view preview period is expected to run until March 15, 2020, although it might be extended\.
+ For any questions, issues, or feedback related to the preview features during the preview period, email `redshiftpreviews@amazon.com` or open a support case with AWS Support\. 

For details about SQL commands used to create and manage materialized views, see the following command topics:
+ [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)
+ [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)
+ [DROP MATERIALIZED VIEW](materialized-view-drop-sql-command.md)

For information about system tables and views to monitor materialized views, see the following topics: 
+ [STV\_MV\_INFO](r_STV_MV_INFO.md)
+ [STL\_MV\_STATE](r_STL_MV_STATE.md)
+ [SVL\_MV\_REFRESH\_STATUS](r_SVL_MV_REFRESH_STATUS.md)

## Querying a Materialized View<a name="materialized-view-query"></a>

You can use a materialized view in any SQL query by referencing the materialized view name as the data source, like a table or standard view\. There are no limitations to using materialized views in queries\. 

When a query accesses a materialized view, it sees only the data that is stored in the materialized view as of its most recent refresh\. Thus, the query might not see all latest changes from corresponding base tables of the materialized view\.

**Note**  
If other users want to query the materialized view, the owner of the materialized view grants the SELECT privilege to those users\. The other users don't need to have the SELECT privilege on the underlying base tables\.  
The owner of the materialized view can also revoke the SELECT privilege from other users, to prevent them from querying the materialized view\.  
If the owner of the materialized view no longer has the SELECT privilege on the underlying base tables, then the following is true:  
The owner can no longer query the materialized view\.
Other users who have the SELECT privilege on the materialized view can no longer query the materialized view\.

The following example queries the `tickets_mv` materialized view\. For more information on the SQL command used to create a materialized view, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\.

```
SELECT sold
FROM tickets_mv
WHERE catgroup = 'Concerts';
```

Because the query results are precomputed, there's no need to access the underlying tables \(`category`, `event`, and `sales`\)\. Amazon Redshift can return the results directly from `tickets_mv`\.