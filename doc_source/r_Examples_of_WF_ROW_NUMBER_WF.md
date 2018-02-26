# ROW\_NUMBER Window Function Example<a name="r_Examples_of_WF_ROW_NUMBER_WF"></a>

The following example partitions the table by SELLERID and orders each partition by QTY \(in ascending order\), then assigns a row number to each row\. The results are sorted after the window function results are applied\. 

```
select salesid, sellerid, qty, 
row_number() over 
(partition by sellerid
 order by qty asc) as row
from winsales
order by 2,4;

 salesid | sellerid | qty | row
---------+----------+-----+-----
   10006 |        1 |  10 |   1
   10001 |        1 |  10 |   2
   10005 |        1 |  30 |   3
   20001 |        2 |  20 |   1
   20002 |        2 |  20 |   2
   30001 |        3 |  10 |   1
   30003 |        3 |  15 |   2
   30004 |        3 |  20 |   3
   30007 |        3 |  30 |   4
   40005 |        4 |  10 |   1
   40001 |        4 |  40 |   2
(11 rows)
```

 For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\. 