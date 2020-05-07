# SQRT function<a name="r_SQRT"></a>

 The SQRT function returns the square root of a numeric value\. 

## Syntax<a name="r_SQRT-synopsis"></a>

```
SQRT (expression)
```

## Argument<a name="r_SQRT-argument"></a>

 *expression*   
The expression must have an integer, decimal, or floating\-point data type\. 

## Return type<a name="r_SQRT-return-type"></a>

SQRT returns a DOUBLE PRECISION number\. 

## Examples<a name="r_SQRT-examples"></a>

The following example returns the square root for some COMMISSION values from the SALES table\. The COMMISSION column is a DECIMAL column\. 

```
select sqrt(commission)
from sales where salesid <10 order by salesid;

sqrt
------------------
10.4498803820905
3.37638860322683
7.24568837309472
5.1234753829798
...
```

The following query returns the rounded square root for the same set of COMMISSION values\. 

```
select salesid, commission, round(sqrt(commission))
from sales where salesid <10 order by salesid;

salesid | commission | round
--------+------------+-------
      1 |     109.20 |    10
      2 |      11.40 |     3
      3 |      52.50 |     7
      4 |      26.25 |     5
...
```