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

The execution plan for a specific Amazon Redshift query statement breaks down execution and calculation of a query into a discrete sequence of steps and table operations that eventually produce a final result set for the query\. The following table provides a summary of steps that Amazon Redshift can use in developing an execution plan for any query a user submits for execution\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_EXPLAIN.html)

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