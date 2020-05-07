# DENSE\_RANK window function examples<a name="r_Examples_of_dense_rank_WF"></a>

## Dense ranking with ORDER BY<a name="r_Examples_of_dense_rank_WF-dense-ranking-with-order-by-"></a>

Order the table by the quantity sold \(in descending order\), and assign both a dense rank and a regular rank to each row\. The results are sorted after the window function results are applied\. 

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

Note the difference in rankings assigned to the same set of rows when the DENSE\_RANK and RANK functions are used side by side in the same query\. For a description of the WINSALES table, see [Window function examples](r_Window_function_examples.md)\.

## Dense ranking with PARTITION BY and ORDER BY<a name="r_Examples_of_dense_rank_WF-dense-ranking-with-partition-by-and-order-by"></a>

Partition the table by SELLERID and order each partition by the quantity \(in descending order\) and assign a dense rank to each row\. The results are sorted after the window function results are applied\. 

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

 For a description of the WINSALES table, see [Window function examples](r_Window_function_examples.md)\.