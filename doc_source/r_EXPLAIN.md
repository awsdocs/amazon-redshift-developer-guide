# EXPLAIN<a name="r_EXPLAIN"></a>

Displays the execution plan for a query statement without running the query\.

## Syntax<a name="r_EXPLAIN-synopsis"></a>

```
EXPLAIN [ VERBOSE ] query
```

## Parameters<a name="r_EXPLAIN-parameters"></a>

VERBOSE   
Displays the full query plan instead of just a summary\.

 *query*   
Query statement to explain\. The query can be a SELECT, INSERT, CREATE TABLE AS, UPDATE, or DELETE statement\.

## Usage notes<a name="r_EXPLAIN-usage-notes"></a>

EXPLAIN performance is sometimes influenced by the time it takes to create temporary tables\. For example, a query that uses the common subexpression optimization requires temporary tables to be created and analyzed in order to return the EXPLAIN output\. The query plan depends on the schema and statistics of the temporary tables\. Therefore, the EXPLAIN command for this type of query might take longer to run than expected\.

You can use EXPLAIN only for the following commands:
+ SELECT
+ SELECT INTO
+ CREATE TABLE AS
+ INSERT
+ UPDATE
+ DELETE

The EXPLAIN command will fail if you use it for other SQL commands, such as data definition language \(DDL\) or database operations\.

## Query planning and execution steps<a name="r_EXPLAIN-query-planning-and-execution-steps"></a>

The execution plan for a specific Amazon Redshift query statement breaks down execution and calculation of a query into a discrete sequence of steps and table operations that eventually produce a final result set for the query\. For information about query planning, see [Query processing](c-query-processing.md)\.

The following table provides a summary of steps that Amazon Redshift can use in developing an execution plan for any query a user submits for execution\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_EXPLAIN.html)

## Using EXPLAIN for RLS<a name="r_EXPLAIN-RLS"></a>

If a query contains a table that is subject to row\-level security \(RLS\) policies, EXPLAIN displays a special RLS SecureScan node\. Amazon Redshift also logs the same node type to the STL\_EXPLAIN system table\. EXPLAIN doesn't reveal the RLS predicate that applies to dim\_tbl\. The RLS SecureScan node type serves as an indicator that the execution plan contains additional operations that are invisible to the current user\.

The following example illustrates an RLS SecureScan node\.

```
EXPLAIN
SELECT D.cint
FROM fact_tbl F INNER JOIN dim_tbl D ON F.k_dim = D.k
WHERE F.k_dim / 10 > 0;
                               QUERY PLAN                               
------------------------------------------------------------------------
 XN Hash Join DS_DIST_ALL_NONE  (cost=0.08..0.25 rows=1 width=4)
   Hash Cond: ("outer".k_dim = "inner"."k")
   ->  *XN* *RLS SecureScan f  (cost=0.00..0.14 rows=2 width=4)*
         Filter: ((k_dim / 10) > 0)
   ->  XN Hash  (cost=0.07..0.07 rows=2 width=8)
         ->  XN Seq Scan on dim_tbl d  (cost=0.00..0.07 rows=2 width=8)
               Filter: (("k" / 10) > 0)
```

To enable full investigation of query plans that are subject to RLS, Amazon Redshift offers the EXPLAIN RLS system permissions\. Users that have been granted this permission can inspect complete query plans that also include RLS predicates\. 

The following example illustrates an additional Seq Scan below the RLS SecureScan node also includes the RLS policy predicate \(k\_dim > 1\)\.

```
EXPLAIN SELECT D.cint
FROM fact_tbl F INNER JOIN dim_tbl D ON F.k_dim = D.k
WHERE F.k_dim / 10 > 0;
                                   QUERY PLAN                                    
---------------------------------------------------------------------------------
 XN Hash Join DS_DIST_ALL_NONE  (cost=0.08..0.25 rows=1 width=4)
   Hash Cond: ("outer".k_dim = "inner"."k")
   *->  XN RLS SecureScan f  (cost=0.00..0.14 rows=2 width=4)
         Filter: ((k_dim / 10) > 0)*
         ->  *XN* *Seq Scan on fact_tbl rls_table  (cost=0.00..0.06 rows=5 width=8)
               Filter: (k_dim > 1)*
   ->  XN Hash  (cost=0.07..0.07 rows=2 width=8)
         ->  XN Seq Scan on dim_tbl d  (cost=0.00..0.07 rows=2 width=8)
               Filter: (("k" / 10) > 0)
```

While the EXPLAIN RLS permission is granted to a user, Amazon Redshift logs the full query plan including RLS predicates in the STL\_EXPLAIN system table\. Queries that are run while this permission is not granted will be logged without RLS internals\. Granting or removing the EXPLAIN RLS permission won't change what Amazon Redshift has logged to STL\_EXPLAIN for previous queries\.

### AWS Lake Formation\-RLS protected Redshift relations<a name="r_EXPLAIN_RLS-LF"></a>

The following example illustrates an LF SecureScan node, which you can use to view Lake Formation\-RLS relations\.

```
EXPLAIN
SELECT * 
FROM lf_db.public.t_share
WHERE a > 1;
QUERY PLAN
---------------------------------------------------------------
XN LF SecureScan t_share  (cost=0.00..0.02 rows=2 width=11)
(2 rows)
```

## Examples<a name="r_EXPLAIN-examples"></a>

**Note**  
For these examples, the sample output might vary depending on Amazon Redshift configuration\.

The following example returns the query plan for a query that selects the EVENTID, EVENTNAME, VENUEID, and VENUENAME from the EVENT and VENUE tables:

```
explain
select eventid, eventname, event.venueid, venuename
from event, venue
where event.venueid = venue.venueid;
```

```
                                QUERY PLAN
--------------------------------------------------------------------------
XN Hash Join DS_DIST_OUTER  (cost=2.52..58653620.93 rows=8712 width=43)
Hash Cond: ("outer".venueid = "inner".venueid)
->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=23)
->  XN Hash  (cost=2.02..2.02 rows=202 width=22)
->  XN Seq Scan on venue  (cost=0.00..2.02 rows=202 width=22)
(5 rows)
```

The following example returns the query plan for the same query with verbose output:

```
explain verbose
select eventid, eventname, event.venueid, venuename
from event, venue
where event.venueid = venue.venueid;
```

```
                                QUERY PLAN
--------------------------------------------------------------------------
{HASHJOIN
:startup_cost 2.52
:total_cost 58653620.93
:plan_rows 8712
:plan_width 43
:best_pathkeys <>
:dist_info DS_DIST_OUTER
:dist_info.dist_keys (
TARGETENTRY
{
VAR
:varno 2
:varattno 1
...

XN Hash Join DS_DIST_OUTER  (cost=2.52..58653620.93 rows=8712 width=43)
Hash Cond: ("outer".venueid = "inner".venueid)
->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=23)
->  XN Hash  (cost=2.02..2.02 rows=202 width=22)
->  XN Seq Scan on venue  (cost=0.00..2.02 rows=202 width=22)
(519 rows)
```

The following example returns the query plan for a CREATE TABLE AS \(CTAS\) statement: 

```
explain create table venue_nonulls as
select * from venue
where venueseats is not null;

QUERY PLAN
-----------------------------------------------------------
XN Seq Scan on venue  (cost=0.00..2.02 rows=187 width=45)
Filter: (venueseats IS NOT NULL)
(2 rows)
```