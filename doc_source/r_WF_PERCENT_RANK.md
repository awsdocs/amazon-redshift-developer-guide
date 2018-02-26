# PERCENT\_RANK Window Function<a name="r_WF_PERCENT_RANK"></a>

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
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique Ordering of Data for Window Functions](r_Examples_order_by_WF.md)\. 

## Return Type<a name="r_WF_PERCENT_RANK-return-type"></a>

FLOAT8

## Examples<a name="r_WF_PERCENT_RANK-examples"></a>

See [PERCENT\_RANK Window Function Examples](r_Examples_of_PERCENT_RANK_WF.md)\. 