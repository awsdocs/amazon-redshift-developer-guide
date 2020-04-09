# REFRESH MATERIALIZED VIEW<a name="materialized-view-refresh-sql-command"></a>

Refreshes a materialized view\.

When you create a materialized view, its contents reflect the state of the underlying database table or tables at that time\. The data in the materialized view remains unchanged, even when applications make changes to the data in the underlying tables\. To update the data in the materialized view, you can use the `REFRESH MATERIALIZED VIEW` statement at any time\. When you use this statement, Amazon Redshift identifies changes that have taken place in the base table or tables, and then applies those changes to the materialized view\.

Amazon Redshift has two strategies for refreshing a materialized view: 
+ In many cases, Amazon Redshift can perform an incremental refresh\. In an *incremental refresh*, Amazon Redshift quickly identifies the changes to the data in the base tables since last refresh and updates the data in the materialized view\. Incremental refresh is supported for the following SQL constructs used in the query when defining the materialized view\. 
  + Contains clauses SELECT, FROM, \[INNER\] JOIN, WHERE, GROUP BY, HAVING\.
  + Contains aggregations, such as SUM and COUNT\.
  + Most built\-in SQL functions, specifically the ones that are immutable, given the same input arguments and always produces the same output\. For a list of SQL functions for which incremental refresh is not supported, see [Limitations for Incremental Refresh](#mv_REFRESH_MARTERIALIZED_VIEW_limitations)\.
+ If an incremental refresh isn't possible, then Amazon Redshift performs a full refresh\. A *full refresh* reruns the underlying SQL statement, replacing all of the data in the materialized view\.
+ Amazon Redshift automatically picks the refresh method for a materialize view depending on the SELECT query used to define the materialized view\.

For more information about materialized views, see [Creating Materialized Views in Amazon Redshift](materialized-view-overview.md)\.

## Syntax<a name="mv_REFRESH_MATERIALIZED_VIEW-synopsis"></a>

```
REFRESH MATERIALIZED VIEW mv_name
```

## Parameters<a name="mv_REFRESH_MATERIALIZED_VIEW-parameters"></a>

*mv\_name*  
The name of the materialized view to be refreshed\.

## Usage Notes<a name="mv_REFRESH_MARTERIALIZED_VIEW_usage"></a>

Only the owner of a materialized view can perform a `REFRESH MATERIALIZED VIEW` operation on that materialized view\. Furthermore, the owner must have SELECT privilege on the underlying base tables to successfully run `REFRESH MATERIALIZED VIEW`\. 

The `REFRESH MATERIALIZED VIEW` command runs as a transaction of its own\. Amazon Redshift transaction semantics are followed to determine what data from base tables is visible to the `REFRESH` command, or when the changes made by the `REFRESH` command are made visible to other transactions running in Amazon Redshift\.
+ For incremental materialized views, `REFRESH MATERIALIZED VIEW` uses only those base table rows that are already committed\. Therefore, if the refresh operation runs after a data manipulation language \(DML\) statement in the same transaction, then changes of that DML statement aren't visible to refresh\. 
+ Furthermore, take a case where a transaction B follows a transaction A\. In such a case, `REFRESH MATERIALIZED VIEW` issued after committing B doesn't see some committed base table rows that are updated by transaction B while the older transaction A is in progress\. These omitted rows are updated by subsequent refresh operations, after transaction A is committed\.
+ For a full refresh of a materialized view, `REFRESH MATERIALIZED VIEW` sees all base table rows visible to the refresh transaction, according to usual Amazon Redshift transaction semantics\. 

Some operations in Amazon Redshift interact with materialized views\. Some of these operations might force a `REFRESH MATERIALIZED VIEW` operation to fully recompute the materialized view\. An operation can force a full refresh even when the materialized view only uses the SQL features that lend themselves to incremental refresh\. For example:
+ Background vacuum operations might be blocked if materialized views aren't refreshed\. After an internally defined threshold period, a vacuum operation is allowed to run\. When this vacuum operation happens, any dependent materialized views are marked for recomputation upon the next refresh \(even if they are incremental\)\. For more information about events and state changes, see [STL\_MV\_STATE](r_STL_MV_STATE.md)\. 
+ Some user\-initiated operations on base tables force a materialized view to be fully recomputed next time that a REFRESH operation is run\. Examples of such operations are a manually invoked VACUUM, a classic resize, an ALTER DISTKEY operation, an ALTER SORTKEY operation, and a truncate operation\. For more information about events and state changes, see [STL\_MV\_STATE](r_STL_MV_STATE.md)\. 

## Limitations for Incremental Refresh<a name="mv_REFRESH_MARTERIALIZED_VIEW_limitations"></a>

Amazon Redshift currently does not support incremental refresh for materialized views that are defined with a query using any of the following SQL elements:
+ OUTER JOIN \(RIGHT, LEFT, or FULL\)\.
+ Set operations: UNION, INTERSECT, EXCEPT, MINUS\.
+ Aggregate functions: AVG, MEDIAN, PERCENTILE\_CONT, MAX, MIN, LISTAGG, STDDEV\_SAMP, STDDEV\_POP, APPROXIMATE COUNT, APPROXIMATE PERCENTILE, and bitwise aggregate functions\.
**Note**  
The COUNT and SUM aggregate functions are supported\.
+ DISTINCT aggregate functions, such as DISTINCT COUNT, DISTINCT SUM, and so on\.
+ Window functions\.
+ System administration functions\. For a list, see [System Administration Functions](r_System_administration_functions.md)\. 
+ System information functions\. For a list, see [System Information Functions](r_System_information_functions.md)\. 
+ Leader node\-only functions: CURRENT\_SCHEMA, CURRENT\_SCHEMAS, HAS\_DATABASE\_PRIVILEGE, HAS\_SCHEMA\_PRIVILEGE, HAS\_TABLE\_PRIVILEGE, AGE, CURRENT\_TIME, CURRENT\_TIMESTAMP, LOCALTIME, NOW\.
+ Date functions: CURRENT\_DATE, DATE, DATE\_PART, DATE\_TRUNC, DATE\_CMP\_TIMESTAMPTZ, SYSDATE, TIMEOFDAY, TO\_TIMESTAMP\.
+ Math functions: RANDOM\.
+ Date type formatting functions: TO\_CHAR WITH TIMESTAMPTZ\. 
+ A query that uses temporary tables for query optimization, such as optimizing common subexpressions\. 
+ Subqueries in any place other than the FROM clause\.

## Examples<a name="mv_REFRESH_MARTERIALIZED_VIEW_examples"></a>

The following example refreshes the `tickets_mv` materialized view\.

```
REFRESH MATERIALIZED VIEW tickets_mv;
```
