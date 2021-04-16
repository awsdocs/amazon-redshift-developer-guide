# PERCENT\_RANK window function<a name="r_WF_PERCENT_RANK"></a>

Calculates the percent rank of a given row\. The percent rank is determined using this formula:

`(x - 1) / (the number of rows in the window or partition - 1)`

where *x* is the rank of the current row\. The following dataset illustrates use of this formula:

```
Row#	Value	Rank	Calculation	PERCENT_RANK
1	15	1	(1-1)/(7-1)	0.0000
2	20	2	(2-1)/(7-1)	0.1666
3	20	2	(2-1)/(7-1)	0.1666
4	20	2	(2-1)/(7-1)	0.1666
5	30	5	(5-1)/(7-1)	0.6666
6	30	5	(5-1)/(7-1)	0.6666
7	40	7	(7-1)/(7-1)	1.0000
```

The return value range is 0 to 1, inclusive\. The first row in any set has a PERCENT\_RANK of 0\.

## Syntax<a name="r_WF_PERCENT_RANK-synopsis"></a>

```
PERCENT_RANK ()
OVER ( 
[ PARTITION BY partition_expression ] 
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_PERCENT_RANK-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER  
A clause that specifies the window partitioning\. The OVER clause cannot contain a window frame specification\.

PARTITION BY *partition\_expression*   
Optional\. An expression that sets the range of records for each group in the OVER clause\.

ORDER BY *order\_list*   
Optional\. The expression on which to calculate percent rank\. The expression must have either a numeric data type or be implicitly convertible to one\. If ORDER BY is omitted, the return value is 0 for all rows\.  
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="r_WF_PERCENT_RANK-return-type"></a>

FLOAT8

## Examples<a name="r_WF_PERCENT_RANK-examples"></a>

The following example calculates the percent rank of the sales quantities for each seller:

```
select sellerid, qty, percent_rank() 
over (partition by sellerid order by qty) 
from winsales;

sellerid	qty		percent_rank
----------------------------------------
1		10.00		0.0
1		10.64		0.5
1		30.37		1.0
3		10.04		0.0
3		15.15		0.33
3		20.75		0.67
3		30.55		1.0
2		20.09		0.0
2		20.12		1.0
4		10.12		0.0
4		40.23		1.0
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.