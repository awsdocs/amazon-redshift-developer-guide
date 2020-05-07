# IN condition<a name="r_in_condition"></a>

An IN condition tests a value for membership in a set of values or in a subquery\. 

## Syntax<a name="r_in_condition-synopsis"></a>

```
expression [ NOT ] IN (expr_list | table_subquery)
```

## Arguments<a name="r_in_condition-arguments"></a>

 *expression*   
A numeric, character, or datetime expression that is evaluated against the *expr\_list* or *table\_subquery* and must be compatible with the data type of that list or subquery\. 

 *expr\_list*   
One or more comma\-delimited expressions, or one or more sets of comma\-delimited expressions bounded by parentheses\. 

 *table\_subquery*   
A subquery that evaluates to a table with one or more rows, but is limited to only one column in its select list\. 

IN \| NOT IN   
IN returns true if the expression is a member of the expression list or query\. NOT IN returns true if the expression is not a member\. IN and NOT IN return NULL and no rows are returned in the following cases: If *expression* yields null; or if there are no matching *expr\_list* or *table\_subquery* values and at least one of these comparison rows yields null\. 

## Examples<a name="r_in_condition-examples"></a>

The following conditions are true only for those values listed: 

```
qtysold in (2, 4, 5)
date.day in ('Mon', 'Tues')
date.month not in ('Oct', 'Nov', 'Dec')
```

## Optimization for Large IN Lists<a name="r_in_condition-optimization-for-large-in-lists"></a>

To optimize query performance, an IN list that includes more than 10 values is internally evaluated as a scalar array\. IN lists with fewer than 10 values are evaluated as a series of OR predicates\. This optimization is supported for SMALLINT, INTEGER, BIGINT, REAL, DOUBLE PRECISION, BOOLEAN, CHAR, VARCHAR, DATE, TIMESTAMP, and TIMESTAMPTZ data types\.  

Look at the EXPLAIN output for the query to see the effect of this optimization\. For example: 

```
explain select * from sales
QUERY PLAN
--------------------------------------------------------------------
XN Seq Scan on sales  (cost=0.00..6035.96 rows=86228 width=53)
Filter: (salesid = ANY ('{1,2,3,4,5,6,7,8,9,10,11}'::integer[]))
(2 rows)
```