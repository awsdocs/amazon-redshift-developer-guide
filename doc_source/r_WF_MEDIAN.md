# MEDIAN window function<a name="r_WF_MEDIAN"></a>

Calculates the median value for the range of values in a window or partition\. NULL values in the range are ignored\.

MEDIAN is an inverse distribution function that assumes a continuous distribution model\.

MEDIAN is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_WF_MEDIAN-synopsis"></a>

```
MEDIAN ( median_expression )
OVER ( [ PARTITION BY partition_expression ] )
```

## Arguments<a name="r_WF_MEDIAN-arguments"></a>

 *median\_expression*   
An expression, such as a column name, that provides the values for which to determine the median\. The expression must have either a numeric or datetime data type or be implicitly convertible to one\.

OVER   
A clause that specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

PARTITION BY *partition\_expression*   
Optional\. An expression that sets the range of records for each group in the OVER clause\.

## Data types<a name="r_WF_MEDIAN-data-types"></a>

The return type is determined by the data type of *median\_expression*\. The following table shows the return type for each *median\_expression* data type\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_WF_MEDIAN.html)

## Usage notes<a name="r_WF_MEDIAN-data-usage-notes"></a>

If the *median\_expression* argument is a DECIMAL data type defined with the maximum precision of 38 digits, it is possible that MEDIAN will return either an inaccurate result or an error\. If the return value of the MEDIAN function exceeds 38 digits, the result is truncated to fit, which causes a loss of precision\. If, during interpolation, an intermediate result exceeds the maximum precision, a numeric overflow occurs and the function returns an error\. To avoid these conditions, we recommend either using a data type with lower precision or casting the *median\_expression* argument to a lower precision\. 

For example, a SUM function with a DECIMAL argument returns a default precision of 38 digits\. The scale of the result is the same as the scale of the argument\. So, for example, a SUM of a DECIMAL\(5,2\) column returns a DECIMAL\(38,2\) data type\.

The following example uses a SUM function in the *median\_expression* argument of a MEDIAN function\. The data type of the PRICEPAID column is DECIMAL \(8,2\), so the SUM function returns DECIMAL\(38,2\)\.

```
select salesid, sum(pricepaid), median(sum(pricepaid)) 
over() from sales where salesid < 10 group by salesid;
```

To avoid a potential loss of precision or an overflow error, cast the result to a DECIMAL data type with lower precision, as the following example shows\.

```
select salesid, sum(pricepaid), median(sum(pricepaid)::decimal(30,2)) 
over() from sales where salesid < 10 group by salesid;
```

## Examples<a name="r_WF_MEDIAN-examples"></a>

 The following example calculates the median sales quantity for each seller: 

```
select sellerid, qty, median(qty) 
over (partition by sellerid) 
from winsales
order by sellerid;


sellerid	qty	median
---------------------------
1		10	10.0
1		10	10.0
1		30	10.0
2		20	20.0
2		20	20.0
3		10	17.5
3		15	17.5
3		20	17.5
3		30	17.5
4		10	25.0
4		40	25.0
```

For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 