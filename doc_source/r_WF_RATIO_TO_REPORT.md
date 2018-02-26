# RATIO\_TO\_REPORT Window Function<a name="r_WF_RATIO_TO_REPORT"></a>

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

## Return Type<a name="r_WF_RATIO_TO_REPORT-return-type"></a>

FLOAT8

## Examples<a name="r_WF_RATIO_TO_REPORT-examples"></a>

See [RATIO\_TO\_REPORT Window Function Examples](r_Examples_of_RATIO_TO_REPORT_WF.md)\. 