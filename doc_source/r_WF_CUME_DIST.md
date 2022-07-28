# CUME\_DIST window function<a name="r_WF_CUME_DIST"></a>

Calculates the cumulative distribution of a value within a window or partition\. Assuming ascending ordering, the cumulative distribution is determined using this formula:

`count of rows with values <= x / count of rows in the window or partition`

where *x* equals the value in the current row of the column specified in the ORDER BY clause\. The following dataset illustrates use of this formula:

```
Row#	Value	  Calculation    CUME_DIST
1        2500	   (1)/(5)	   0.2
2        2600	   (2)/(5)	   0.4
3        2800	   (3)/(5)	   0.6
4        2900	   (4)/(5)	   0.8
5        3100	   (5)/(5)	   1.0
```

The return value range is >0 to 1, inclusive\.

## Syntax<a name="r_WF_CUME_DIST-synopsis"></a>

```
CUME_DIST ()
OVER ( 
[ PARTITION BY partition_expression ] 
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_CUME_DIST-arguments"></a>

OVER  
A clause that specifies the window partitioning\. The OVER clause cannot contain a window frame specification\.

PARTITION BY *partition\_expression*   
Optional\. An expression that sets the range of records for each group in the OVER clause\.

ORDER BY *order\_list*   
The expression on which to calculate cumulative distribution\. The expression must have either a numeric data type or be implicitly convertible to one\. If ORDER BY is omitted, the return value is 1 for all rows\.   
If ORDER BY doesn't produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="r_WF_CUME_DIST-returns"></a>

FLOAT8

## Examples<a name="r_WF_CUME_DIST-examples"></a>

The following example calculates the cumulative distribution of the quantity for each seller:

```
select sellerid, qty, cume_dist() 
over (partition by sellerid order by qty) 
from winsales;

sellerid   qty	   cume_dist
--------------------------------------------------
1         10.00	   0.33
1         10.64	   0.67
1         30.37	   1
3         10.04	   0.25
3         15.15	   0.5
3         20.75	   0.75
3         30.55	   1
2         20.09	   0.5
2         20.12	   1
4         10.12	   0.5
4         40.23	   1
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.