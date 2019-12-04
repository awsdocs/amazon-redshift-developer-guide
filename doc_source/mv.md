# Materialized Views in Amazon Redshift \(Preview\)<a name="mv"></a>


|  | 
| --- |
| This is prerelease documentation for the materialized view feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

In a data warehouse environment, applications often need to perform complex queries on large tables—for example, `SELECT` statements that perform multiple\-table joins and aggregations on tables that contain millions of rows\. Processing these queries can be expensive, in terms of system resources and the time it takes to return the results\.

Materialized views in Amazon Redshift provide a way to address these issues\. A *materialized view* contains a precomputed result set, based on a SQL query over one or more base tables\. You can issue SELECT statements to query a materialized view, in the same way that you can query other tables or views in the database\. Amazon Redshift returns the precomputed results from the materialized view, without having to access the base tables at all\. From the user standpoint, the query results are returned much faster compared to when retrieving the same data from the base tables\.

Materialized views are especially useful for speeding up queries that are predictable and repeated\. Instead of performing resource\-intensive queries against large database tables \(such as aggregates or multiple\-table joins\), applications can query a materialized view and retrieve a precomputed result set\. For example, consider an extract, transfer, load \(ETL\) or business intelligence \(BI\) pipeline that performs stages of computations over very large datasets\. Such a pipeline might need to run periodic batch jobs to load and transform the original data, using similar SQL statements during each run\. This use case is ideal for a materialized view, because the queries are predictable and repeated over and over again\.

When you create a materialized view, Amazon Redshift runs the user\-specified SQL statement to gather the data from the base table or tables and store the result set\.  The result set eventually becomes stale, as data is inserted, updated, and deleted in the base table or tables\. To address this issue, you can refresh the materialized view at any time\. During a refresh operation, Amazon Redshift determines the best way to update the data in the materialized view\.
+ In many cases, Amazon Redshift can perform an incremental refresh\. In an *incremental refresh, *changes to the data in the base tables are quickly identified, and then the data in the materialized view is updated with these changes\.
+ If an incremental refresh isn't possible, then Amazon Redshift performs a full refresh\. A *full refresh* reruns the underlying SQL statement, replacing all of the data in the materialized view\.

To use materialized views:
+ To use materialized view, use the new Amazon Redshift console\.
+ To use materialized views, you must confirm that your Amazon Redshift cluster is using the cluster maintenance track for **Preview** with the track named **preview\_features**\. The current cluster maintenance version for the preview track is `1.0.11532`\. For more information, see [Choosing Cluster Maintenance Tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks) in the [Amazon Redshift Cluster Management Guide](https://docs.aws.amazon.com/redshift/latest/mgmt/)\.
+ Create a cluster to work with the preview\. For more information, see [Creating a Cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the [Amazon Redshift Cluster Management Guide](https://docs.aws.amazon.com/redshift/latest/mgmt/)\.
+ The materialized view preview period is expected to run until January 31, 2020\. Although it might be extended\.
+ For any questions, issues, or feedback related to the preview features during the preview period, email `redshiftpreviews@amazon.com` or open a support case with AWS Support\. 

**Topics**
+ [CREATE MATERIALIZED VIEW](mv-create.md)
+ [Querying a Materialized View](mv.query.md)
+ [REFRESH MATERIALIZED VIEW](mv-refresh.md)
+ [DROP MATERIALIZED VIEW](mv-drop.md)
+ [Limitations and Usage Notes](mv-usage-notes.md)