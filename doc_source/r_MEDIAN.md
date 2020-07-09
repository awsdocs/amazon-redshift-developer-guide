# MEDIAN function<a name="r_MEDIAN"></a>

Calculates the median value for the range of values\. NULL values in the range are ignored\.

MEDIAN is an inverse distribution function that assumes a continuous distribution model\.

MEDIAN is a special case of [PERCENTILE\_CONT](r_PERCENTILE_CONT.md)\(\.5\)\.

MEDIAN is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_MEDIAN-synopsis"></a>

```
MEDIAN ( median_expression )
```

## Arguments<a name="r_MEDIAN-arguments"></a>

 *median\_expression*   
The target column or expression that the function operates on\.

## Data types<a name="r_MEDIAN-data-types"></a>

The return type is determined by the data type of *median\_expression*\. The following table shows the return type for each *median\_expression* data type\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_MEDIAN.html)

## Usage notes<a name="r_MEDIAN-data-type-usage-notes"></a>

If the *median\_expression* argument is a DECIMAL data type defined with the maximum precision of 38 digits, it is possible that MEDIAN will return either an inaccurate result or an error\. If the return value of the MEDIAN function exceeds 38 digits, the result is truncated to fit, which causes a loss of precision\. If, during interpolation, an intermediate result exceeds the maximum precision, a numeric overflow occurs and the function returns an error\. To avoid these conditions, we recommend either using a data type with lower precision or casting the *median\_expression* argument to a lower precision\. 

If a statement includes multiple calls to sort\-based aggregate functions \(LISTAGG, PERCENTILE\_CONT, or MEDIAN\), they must all use the same ORDER BY values\. Note that MEDIAN applies an implicit order by on the expression value\. 

For example, the following statement returns an error\. 

```
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (pricepaid)
from sales group by salesid, pricepaid;

An error occurred when executing the SQL command:
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (pricepaid)
from sales group by salesid, pricepai...

ERROR: within group ORDER BY clauses for aggregate functions must be the same
```

The following statement executes successfully\. 

```
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (salesid)
from sales group by salesid, pricepaid;
```

## Examples<a name="r_MEDIAN-examples"></a>

The following example shows that MEDIAN produces the same results as PERCENTILE\_CONT\(0\.5\)\. 

```
select top 10  distinct sellerid, qtysold, 
percentile_cont(0.5) within group (order by qtysold),
median (qtysold) 
from sales
group by sellerid, qtysold;

sellerid | qtysold | percentile_cont | median
---------+---------+-----------------+-------
       1 |       1 |             1.0 |    1.0
       2 |       3 |             3.0 |    3.0
       5 |       2 |             2.0 |    2.0
       9 |       4 |             4.0 |    4.0
      12 |       1 |             1.0 |    1.0
      16 |       1 |             1.0 |    1.0
      19 |       2 |             2.0 |    2.0
      19 |       3 |             3.0 |    3.0
      22 |       2 |             2.0 |    2.0
      25 |       2 |             2.0 |    2.0
```