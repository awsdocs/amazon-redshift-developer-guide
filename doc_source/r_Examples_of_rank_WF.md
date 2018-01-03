# RANK Window Function Examples<a name="r_Examples_of_rank_WF"></a>

## Ranking with ORDER BY<a name="r_Examples_of_rank_WF-ranking-with-order-by"></a>

Order the table by the quantity sold \(default ascending\), and assign a rank to each row\. The results are sorted after the window function results are applied: 

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

Note that the outer ORDER BY clause in this example includes columns 2 and 1 to make sure that Amazon Redshift returns consistently sorted results each time this query is run\. For example, rows with sales IDs 10001 and 10006 have identical QTY and RNK values\. Ordering the final result set by column 1 ensures that row 10001 always falls before 10006\. For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\.

## Ranking with PARTITION BY and ORDER BY<a name="r_Examples_of_rank_WF-ranking-with-partition-by-and-order-by"></a>

In this example, the ordering is reversed for the window function \(`order by qty desc`\)\. Now the highest rank value applies to the highest QTY value\. 

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

For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\. 

Partition the table by SELLERID and order each partition by the quantity \(in descending order\) and assign a rank to each row\. The results are sorted after the window function results are applied\. 

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