# COUNT window function<a name="r_WF_COUNT"></a>

 The COUNT window function counts the rows defined by the expression\.

The COUNT function has two variations\. COUNT\(\*\) counts all the rows in the target table whether they include nulls or not\. COUNT\(expression\) computes the number of rows with non\-NULL values in a specific column or expression\.

## Syntax<a name="r_WF_COUNT-synopsis"></a>

```
COUNT ( * | [ ALL ] expression) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list 
                        frame_clause ]
)
```

## Arguments<a name="r_WF_COUNT-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

ALL   
With the argument ALL, the function retains all duplicate values from the expression for counting\. ALL is the default\. DISTINCT is not supported\.

OVER   
Specifies the window clauses for the aggregation functions\. The OVER clause distinguishes window aggregation functions from normal set aggregation functions\.

PARTITION BY *expr\_list*   
Defines the window for the COUNT function in terms of one or more expressions\.

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY is specified, ORDER BY uses the entire table\.

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window function syntax summary](r_Window_function_synopsis.md)\.

## Data types<a name="c_Supported_data_types_wf_count"></a>

The COUNT function supports all argument data types\.

The return type supported by the COUNT function is BIGINT\.

## Examples<a name="r_WF_COUNT-examples"></a>

 The following example shows the sales ID, quantity, and count of all rows from the beginning of the data window: 

```
select salesid, qty,
count(*) over (order by salesid rows unbounded preceding) as count
from winsales
order by salesid;

salesid | qty | count
---------+-----+-----
10001 |  10 |   1
10005 |  30 |   2
10006 |  10 |   3
20001 |  20 |   4
20002 |  20 |   5
30001 |  10 |   6
30003 |  15 |   7
30004 |  20 |   8
30007 |  30 |   9
40001 |  40 |   10
40005 |  10 |   11
(11 rows)
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 

The following example shows how the sales ID, quantity, and count of non\-null rows from the beginning of the data window\. \(In the WINSALES table, the QTY\_SHIPPED column contains some NULLs\.\) 

```
select salesid, qty, qty_shipped,
count(qty_shipped)
over (order by salesid rows unbounded preceding) as count
from winsales
order by salesid;

salesid | qty | qty_shipped | count
---------+-----+-------------+-------
10001 |  10 |          10 |   1
10005 |  30 |             |   1
10006 |  10 |             |   1
20001 |  20 |          20 |   2
20002 |  20 |          20 |   3
30001 |  10 |          10 |   4
30003 |  15 |             |   4
30004 |  20 |             |   4
30007 |  30 |             |   4
40001 |  40 |             |   4
40005 |  10 |          10 |   5
(11 rows)
```