# ROW\_NUMBER window function<a name="r_WF_ROW_NUMBER"></a>

Determines the ordinal number of the current row within a group of rows, counting from 1, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the ordinal numbers are reset for each group of rows\. Rows with equal values for the ORDER BY expressions receive the different row numbers nondeterministically\. 

## Syntax<a name="r_WF_ROW_NUMBER-synopsis"></a>

```
ROW_NUMBER () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_ROW_NUMBER-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the ROW\_NUMBER function\. 

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the ROW\_NUMBER function\. 

ORDER BY *order\_list*   
Optional\. The expression that defines the columns on which the row numbers are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\.    
If ORDER BY does not produce a unique ordering or is omitted, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="c_Supported_data_types_r_WF_ROW_NUMBER"></a>

BIGINT

## Examples<a name="r_WF_ROW_NUMBER-examples"></a>

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

 For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 