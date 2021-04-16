# RANK window function<a name="r_WF_RANK"></a>

 The RANK window function determines the rank of a value in a group of values, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the rankings are reset for each group of rows\. Rows with equal values for the ranking criteria receive the same rank\. Amazon Redshift adds the number of tied rows to the tied rank to calculate the next rank and thus the ranks might not be consecutive numbers\. For example, if two rows are ranked 1, the next rank is 3\. 

 RANK differs from the [DENSE\_RANK window function](r_WF_DENSE_RANK.md) in one respect: For DENSE\_RANK, if two or more rows tie, there is no gap in the sequence of ranked values\. For example, if two rows are ranked 1, the next rank is 2\.

You can have ranking functions with different PARTITION BY and ORDER BY clauses in the same query\. 

## Syntax<a name="r_WF_RANK-synopsis"></a>

```
RANK () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_RANK-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the RANK function\.

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the window\. 

ORDER BY *order\_list*   
Optional\. Defines the columns on which the ranking values are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\. If ORDER BY is omitted, the return value is 1 for all rows\.   
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="c_Supported_data_types_wf_rank"></a>

INTEGER

## Examples<a name="r_WF_RANK-examples"></a>

The following example orders the table by the quantity sold \(default ascending\), and assign a rank to each row\. A rank value of 1 is the highest ranked value\. The results are sorted after the window function results are applied: 

```
select salesid, qty,
rank() over (order by qty) as rnk
from winsales
order by 2,1;

salesid | qty | rnk
--------+-----+-----
10001 |  10 |  1
10006 |  10 |  1
30001 |  10 |  1
40005 |  10 |  1
30003 |  15 |  5
20001 |  20 |  6
20002 |  20 |  6
30004 |  20 |  6
10005 |  30 |  9
30007 |  30 |  9
40001 |  40 |  11
(11 rows)
```

Note that the outer ORDER BY clause in this example includes columns 2 and 1 to make sure that Amazon Redshift returns consistently sorted results each time this query is run\. For example, rows with sales IDs 10001 and 10006 have identical QTY and RNK values\. Ordering the final result set by column 1 ensures that row 10001 always falls before 10006\. For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.

In the following example, the ordering is reversed for the window function \(`order by qty desc`\)\. Now the highest rank value applies to the largest QTY value\. 

```
select salesid, qty,
rank() over (order by qty desc) as rank
from winsales
order by 2,1;

 salesid | qty | rank
---------+-----+-----
   10001 |  10 |   8
   10006 |  10 |   8
   30001 |  10 |   8
   40005 |  10 |   8
   30003 |  15 |   7
   20001 |  20 |   4
   20002 |  20 |   4
   30004 |  20 |   4
   10005 |  30 |   2
   30007 |  30 |   2
   40001 |  40 |   1
(11 rows)
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 

The following example partitions the table by SELLERID and order each partition by the quantity \(in descending order\) and assign a rank to each row\. The results are sorted after the window function results are applied\. 

```
select salesid, sellerid, qty, rank() over
(partition by sellerid
order by qty desc) as rank
from winsales
order by 2,3,1;

salesid | sellerid | qty | rank
--------+----------+-----+-----
  10001 |        1 |  10 |  2
  10006 |        1 |  10 |  2
  10005 |        1 |  30 |  1
  20001 |        2 |  20 |  1
  20002 |        2 |  20 |  1
  30001 |        3 |  10 |  4
  30003 |        3 |  15 |  3
  30004 |        3 |  20 |  2
  30007 |        3 |  30 |  1
  40005 |        4 |  10 |  2
  40001 |        4 |  40 |  1
(11 rows)
```