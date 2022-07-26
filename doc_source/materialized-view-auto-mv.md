# Automated materialized views<a name="materialized-view-auto-mv"></a>

Materialized views are a powerful tool for improving query performance in Amazon Redshift\. They do this by storing a precomputed result set\. Similar queries don't have to re\-run the same logic each time, because they can pull records from the existing result set\. Developers and analysts create materialized views after analyzing their workloads to determine which queries would benefit and whether the maintenance cost of each materialized view is worthwhile\. As workloads grow or change, these materialized views must be reviewed to ensure they continue to provide tangible performance benefits\.

The Automated Materialized Views \(AutoMV\) feature in Redshift provides the same performance benefits of user\-created materialized views\. Amazon Redshift continually monitors the workload using machine learning and then creates new materialized views when they are beneficial\. AutoMV balances the costs of creating and keeping materialized views up to date against expected benefits to query latency\. The system also monitors previously created AutoMVs and drops them when they are no longer beneficial\.

AutoMV behavior and capabilities are the same as user\-created materialized views\. They are refreshed automatically and incrementally, using the same criteria and restrictions\. Just like materialized views created by users, [Automatic query rewriting to use materialized views](materialized-view-auto-rewrite.md) identifies queries that can benefit from the system\-created AutoMVs and automatically rewrites those to use the AutoMVs, improving query performance\. Developers don't need to change queries to take advantage of AutoMV\.

Any workload with queries that are used repeatedly can benefit from AutoMV\. Common use cases include:
+ *Dashboards* \- Dashboards are widely used to provide quick views of key business indicators \(KPIs\), events, trends, and other metrics\. They often have a common layout with charts and tables, but show different views for filtering, or for dimension\-selection operations, like drill down\. Dashboards often have a common set of queries used repeatedly with different parameters\. Dashboard queries can benefit greatly from automated materialized views\.
+  *Reports* \- Reporting queries may be scheduled at various frequencies, based on business requirements and the type of report\. Additionally, they can be automated or on\-demand\. A common characteristic of reporting queries is that they can be long running and resource\-intensive\. With AutoMV, these queries don't need to be recomputed each time they are run, which reduces runtime for each query and resource utilization in Redshift\. 

  To turn off automated materialized views, you update the `auto_mv` parameter group to `false`\. For more information, see [Amazon Redshiftparameter groups](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html) in the Amazon Redshift Cluster Management Guide\.

## SQL scope and considerations for automated materialized views<a name="materialized-view-auto-mv-important"></a>
+ An automated materialized view can be triggered and created by a query or subquery, provided it contains a `GROUP BY` clause or one of the following aggregate functions: SUM, COUNT, MIN, MAX or AVG\. But it cannot contain any of the following:
  + Left, right, or full outer joins
  + Aggregate functions other than SUM, COUNT, MIN, MAX, and AVG\. \(These particular functions work with automatic query rewriting\.\)
  + Any aggregate function that includes DISTINCT
  + Any window functions
  + SELECT DISTINCT or HAVING clauses
  + External tables, such as datashares and federated tables
  + Other materialized views

  It isn't guaranteed that a query that meets the criteria will trigger the creation of an automated materialized view\. The system determines which candidates to create a view from, based on its expected benefit to the workload and cost in resources to maintain, which includes the cost to the system to refresh\. Each resulting materialized view is guaranteed to be usable by automatic query rewriting\.
+ Even though AutoMV may be triggered by a subquery or individual legs of set operators, the resulting materialized view will not contain subqueries or set operators\.
+ To determine if AutoMV was used for queries, view the EXPLAIN plan and look for `%_auto_mv_%` in the output\. For more information, see [EXPLAIN](https://docs.aws.amazon.com/redshift/latest/dg/r_EXPLAIN.html)\.

## Automated materialized views limitations<a name="materialized-view-auto-mv-limitations"></a>

Following are limitations for working with automated materialized views:
+ *Maximum number of AutoMVs* \- The limit of automated materialized views is 200\.
+ *Storage space and capacity* \- An important characteristic of AutoMV is that it is performed using spare background cycles to ensure that user workloads are not negatively impacted\. If the cluster is busy or running out of storage space, AutoMV ceases its activity\. Specifically, at 80% usage, no new automated materialized views are created\. They may be dropped at 90% usage, to ensure that user workloads continue without performance degradation\.