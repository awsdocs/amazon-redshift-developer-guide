# Refreshing a materialized view<a name="materialized-view-refresh"></a>

When you create a materialized view, its contents reflect the state of the underlying database table or tables at that time\. The data in the materialized view remains unchanged, even when applications make changes to the data in the underlying tables\. To update the data in the materialized view, you can use the `REFRESH MATERIALIZED VIEW` statement at any time to manually refresh materialized views\. When you use this statement, Amazon Redshift identifies changes that have taken place in the base table or tables, and then applies those changes to the materialized view\.

Amazon Redshift has two strategies for refreshing a materialized view: 
+ In many cases, Amazon Redshift can perform an incremental refresh\. In an *incremental refresh*, Amazon Redshift quickly identifies the changes to the data in the base tables since the last refresh and updates the data in the materialized view\. Incremental refresh is supported on the following SQL constructs used in the query when defining the materialized view:
  + Constructs that contain the clauses SELECT, FROM, \[INNER\] JOIN, WHERE, GROUP BY, or HAVING\.
  + Constructs that contain aggregations, such as SUM and COUNT\.
  + Most built\-in SQL functions, specifically the ones that are immutable, given that these have the same input arguments and always produce the same output\. 
+ If an incremental refresh isn't possible, then Amazon Redshift performs a full refresh\. A *full refresh* reruns the underlying SQL statement, replacing all of the data in the materialized view\.
+ Amazon Redshift automatically chooses the refresh method for a materialize view depending on the SELECT query used to define the materialized view\. 

Amazon Redshift currently has the following limitations for incremental refresh for materialized views\. 

Amazon Redshift doesn't support incremental refresh for materialized views that are defined with a query using the following SQL elements:
+ OUTER JOIN \(RIGHT, LEFT, or FULL\)\.
+ The set operations UNION, INTERSECT, EXCEPT, and MINUS\.
+ The aggregate functions AVG, MEDIAN, PERCENTILE\_CONT, MAX, MIN, LISTAGG, STDDEV\_SAMP, STDDEV\_POP, APPROXIMATE COUNT, APPROXIMATE PERCENTILE, and bitwise aggregate functions\.
**Note**  
The COUNT and SUM aggregate functions are supported\.
+ DISTINCT aggregate functions, such as DISTINCT COUNT, DISTINCT SUM, and so on\.
+ Window functions\.
+ A query that uses temporary tables for query optimization, such as optimizing common subexpressions\.
+ Subqueries in any place other than the FROM clause\.
+ External tables referenced as base tables in the query that defines the materialized view\.

## Autorefreshing a materialized view<a name="materialized-view-auto-refresh"></a>

Amazon Redshift can automatically refresh materialized views with up\-to\-date data from its base tables when materialized views are created with or altered to have the autorefresh option\. Amazon Redshift autorefreshes materialized views as soon as possible after base tables changes\.

To complete refresh of the most important materialized views with minimal impact to active workloads in your cluster, Amazon Redshift considers multiple factors\. These factors include current system load, the resources needed for refresh, available cluster resources, and how often the materialized views are used\. 

Amazon Redshift prioritizes your workloads over autorefresh and might stop autorefresh to preserve the performance of user workload\. This approach might delay refresh of some materialized views\. In some cases, you might need more deterministic refresh behavior for your materialized views\. If so, consider using manual refresh as described in [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md) or scheduled refresh using the Amazon Redshift scheduler API operations or the console\.

You can set autorefresh for materialized views using CREATE MATERIALIZED VIEW\. You can also use the AUTO REFRESH clause to refresh materialized views automatically\. For more information about creating materialized views, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\. You can enable autorefresh for a current materialized view by using [ALTER MATERIALIZED VIEW](r_ALTER_MATERIALIZED_VIEW.md)\.

Consider the following when you refresh materialized views:
+ You can still refresh a materialized view explicitly using REFRESH MATERIALIZED VIEW command even if you haven't enabled autorefresh for the materialized view\.
+ Amazon Redshift doesn't autorefresh materialized views defined on external tables\.
+ For refresh status, you can check SVL\_MV\_REFRESH\_STATUS, which records queries that were user\-initiated or autorefreshed\. 
+ To run REFRESH on recompute\-only materialized views, make sure that you have the CREATE privilege on schemas\. For more information, see [GRANT](r_GRANT.md)\.