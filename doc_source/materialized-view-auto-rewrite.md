# Automatic query rewriting to use materialized views<a name="materialized-view-auto-rewrite"></a>

You can use automatic query rewriting of materialized views in Amazon Redshift to have Amazon Redshift rewrite queries to use materialized views\. Doing this accelerates query workloads even for queries that don't explicitly reference a materialized view\. When Amazon Redshift rewrites queries, it only uses materialized views that are up to date\.

## Usage notes<a name="mv_auto-rewrite_usage"></a>

To check if automatic rewriting of queries is used for a query, you can inspect the query plan or STL\_EXPLAIN\. The following shows a SELECT statement and the EXPLAIN output of the original query plan\.

```
SELECT catgroup, SUM(qtysold) AS sold
FROM category c, event e, sales s
WHERE c.catid = e.catid AND e.eventid = s.eventid
GROUP BY 1;

EXPLAIN 
 XN HashAggregate  (cost=920021.24..920021.24 rows=1 width=35)
   ->  XN Hash Join DS_BCAST_INNER  (cost=440004.53..920021.22 rows=4 width=35)
         Hash Cond: ("outer".eventid = "inner".eventid)
         ->  XN Seq Scan on sales s  (cost=0.00..7.40 rows=740 width=6)
         ->  XN Hash  (cost=440004.52..440004.52 rows=1 width=37)
               ->  XN Hash Join DS_BCAST_INNER  (cost=0.01..440004.52 rows=1 width=37)
                     Hash Cond: ("outer".catid = "inner".catid)
                     ->  XN Seq Scan on event e  (cost=0.00..2.00 rows=200 width=6)
                     ->  XN Hash  (cost=0.01..0.01 rows=1 width=35)
                           ->  XN Seq Scan on category c  (cost=0.00..0.01 rows=1 width=35)
```

The following shows the EXPLAIN output after a successful automatic rewriting\. This output includes a scan on the materialized view in the query plan that replaces parts of the original query plan\. 

```
* EXPLAIN 
     XN HashAggregate  (cost=11.85..12.35 rows=200 width=41)
       ->  XN Seq Scan on mv_tbl__tickets_mv__0 derived_table1  (cost=0.00..7.90 rows=790 width=41)
```

Only up\-to\-date \(fresh\) materialized views are considered for automatic rewriting of queries, irrespective of the refresh strategy, such as auto, scheduled, or manual\. Hence, the original query returns up\-to\-date results\. When a materialized view is explicitly referenced in queries, Amazon Redshift accesses currently stored data in the materialized view\. This data might not reflect the latest changes from the base tables of the materialized view\.

You can use automatic query rewriting of materialized views that are created on cluster version 1\.0\.20949 or later\.

You can stop automatic query rewriting at the session level by using SET mv\_enable\_aqmv\_for\_session to FALSE\.

## Limitations<a name="mv_auto-rewrite_limitations"></a>

Following are limitations for using automatic query rewriting of materialized views:
+ Automatic query rewriting works with materialized views that don't reference or include any of the following:
  + Subqueries
  + Left, right, or full outer joins
  + Set operations 
  + Any aggregate functions, except SUM, COUNT, MIN, and MAX\. \(These are the only aggregate functions that work with automatic query rewriting\.\)
  + Any aggregate functions with DISTINCT
  + Any window functions
  + SELECT DISTINCT or HAVING clauses
  + External tables
  + Other materialized views
+ Automatic query rewriting rewrites SELECT queries that refer to user\-defined Amazon Redshift tables\. Amazon Redshift doesn't rewrite the following queries:
  + CREATE TABLE AS statements
  + SELECT INTO statements
  + Queries on catalogs or system tables
  + Queries with outer joins or a SELECT DISTINCT clause
+ If a query isn't automatically rewritten, check whether you have the SELECT privilege on the specified materialized view and the [mv\_enable\_aqmv\_for\_session](r_mv_enable_aqmv_for_session.md) option is set to TRUE\. 

  You can also check if your materialized views are eligible for automatic rewriting of queries by inspecting STV\_MV\_INFO\. For more information, see [STV\_MV\_INFO](r_STV_MV_INFO.md)\.