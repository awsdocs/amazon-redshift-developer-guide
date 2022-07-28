# RATIO\_TO\_REPORT window function<a name="r_WF_RATIO_TO_REPORT"></a>

Calculates the ratio of a value to the sum of the values in a window or partition\. The ratio to report value is determined using the formula:

`value of `*ratio\_expression* `argument for the current row / sum of` *ratio\_expression* `argument for the window or partition`

The following dataset illustrates use of this formula:

```
Row#	Value	Calculation	RATIO_TO_REPORT
1	2500	(2500)/(13900)	0.1798
2	2600	(2600)/(13900)	0.1870
3	2800	(2800)/(13900)	0.2014
4	2900	(2900)/(13900)	0.2086
5	3100	(3100)/(13900)	0.2230
```

The return value range is 0 to 1, inclusive\. If *ratio\_expression* is NULL, then the return value is NULL\.

## Syntax<a name="r_WF_RATIO_TO_REPORT-synopsis"></a>

```
RATIO_TO_REPORT ( ratio_expression )
OVER ( [ PARTITION BY partition_expression ] )
```

## Arguments<a name="r_WF_RATIO_TO_REPORT-arguments"></a>

*ratio\_expression*   
An expression, such as a column name, that provides the value for which to determine the ratio\. The expression must have either a numeric data type or be implicitly convertible to one\.  
You cannot use any other analytic function in *ratio\_expression*\.

OVER  
A clause that specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

PARTITION BY *partition\_expression*   
Optional\. An expression that sets the range of records for each group in the OVER clause\.

## Return type<a name="r_WF_RATIO_TO_REPORT-return-type"></a>

FLOAT8

## Examples<a name="r_WF_RATIO_TO_REPORT-examples"></a>

The following example calculates the ratios of the sales quantities for each seller:

```
select sellerid, qty, ratio_to_report(qty) 
over (partition by sellerid) 
from winsales;

sellerid	qty		ratio_to_report
-------------------------------------------
2		20.12312341      0.5
2		20.08630000      0.5
4		10.12414400      0.2
4		40.23000000      0.8
1		30.37262000      0.6
1		10.64000000      0.21
1		10.00000000      0.2
3		10.03500000      0.13
3		15.14660000      0.2
3		30.54790000      0.4
3		20.74630000      0.27
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\.