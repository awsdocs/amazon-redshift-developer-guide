# DENSE\_RANK window function<a name="r_WF_DENSE_RANK"></a>

The DENSE\_RANK window function determines the rank of a value in a group of values, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the rankings are reset for each group of rows\. Rows with equal values for the ranking criteria receive the same rank\. The DENSE\_RANK function differs from RANK in one respect: If two or more rows tie, there is no gap in the sequence of ranked values\. For example, if two rows are ranked 1, the next rank is 2\. 

You can have ranking functions with different PARTITION BY and ORDER BY clauses in the same query\. 

## Syntax<a name="r_WF_DENSE_RANK-synopsis"></a>

```
DENSE_RANK () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_DENSE_RANK-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the DENSE\_RANK function\. 

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the window\. 

ORDER BY *order\_list*   
Optional\. The expression on which the ranking values are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\. If ORDER BY is omitted, the return value is 1 for all rows\.   
If ORDER BY doesn't produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="c_Supported_data_types_wf_dense_rank"></a>

INTEGER

## Examples<a name="r_WF_DENSE_RANK-examples"></a>

The following example orders the table by the quantity sold \(in descending order\), and assign both a dense rank and a regular rank to each row\. The results are sorted after the window function results are applied\. 

```
select salesid, qty,
dense_rank() over(order by qty desc) as d_rnk,
rank() over(order by qty desc) as rnk
from winsales
order by 2,1;

salesid | qty | d_rnk | rnk
---------+-----+-------+-----
10001 |  10 |     5 |   8
10006 |  10 |     5 |   8
30001 |  10 |     5 |   8
40005 |  10 |     5 |   8
30003 |  15 |     4 |   7
20001 |  20 |     3 |   4
20002 |  20 |     3 |   4
30004 |  20 |     3 |   4
10005 |  30 |     2 |   2
30007 |  30 |     2 |   2
40001 |  40 |     1 |   1
(11 rows)
```

Note the difference in rankings assigned to the same set of rows when the DENSE\_RANK and RANK functions are used side by side in the same query\. For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.

The following example partitions the table by SELLERID and orders each partition by the quantity \(in descending order\) and assign a dense rank to each row\. The results are sorted after the window function results are applied\. 

```
select salesid, sellerid, qty,
dense_rank() over(partition by sellerid order by qty desc) as d_rnk
from winsales
order by 2,3,1;

salesid | sellerid | qty | d_rnk
---------+----------+-----+-------
10001 |        1 |  10 |     2
10006 |        1 |  10 |     2
10005 |        1 |  30 |     1
20001 |        2 |  20 |     1
20002 |        2 |  20 |     1
30001 |        3 |  10 |     4
30003 |        3 |  15 |     3
30004 |        3 |  20 |     2
30007 |        3 |  30 |     1
40005 |        4 |  10 |     2
40001 |        4 |  40 |     1
(11 rows)
```

 For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.