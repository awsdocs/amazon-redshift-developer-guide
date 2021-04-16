# STDDEV\_SAMP and STDDEV\_POP functions<a name="r_STDDEV_functions"></a>

 The STDDEV\_SAMP and STDDEV\_POP functions return the sample and population standard deviation of a set of numeric values \(integer, decimal, or floating\-point\)\. The result of the STDDEV\_SAMP function is equivalent to the square root of the sample variance of the same set of values\. 

STDDEV\_SAMP and STDDEV are synonyms for the same function\. 

## Syntax<a name="r_STDDEV_functions-syntax"></a>

```
STDDEV_SAMP | STDDEV ( [ DISTINCT | ALL ] expression)
STDDEV_POP ( [ DISTINCT | ALL ] expression)
```

The expression must have an integer, decimal, or floating point data type\. Regardless of the data type of the expression, the return type of this function is a double precision number\. 

**Note**  
Standard deviation is calculated using floating point arithmetic, which might result in slight imprecision\.

## Usage notes<a name="r_STDDEV_usage_notes"></a>

When the sample standard deviation \(STDDEV or STDDEV\_SAMP\) is calculated for an expression that consists of a single value, the result of the function is NULL not 0\. 

## Examples<a name="r_STDDEV_functions-examples"></a>

The following query returns the average of the values in the VENUESEATS column of the VENUE table, followed by the sample standard deviation and population standard deviation of the same set of values\. VENUESEATS is an INTEGER column\. The scale of the result is reduced to 2 digits\. 

```
select avg(venueseats),
cast(stddev_samp(venueseats) as dec(14,2)) stddevsamp,
cast(stddev_pop(venueseats) as dec(14,2)) stddevpop
from venue;

avg  | stddevsamp | stddevpop
-------+------------+-----------
17503 |   27847.76 |  27773.20
(1 row)
```

The following query returns the sample standard deviation for the COMMISSION column in the SALES table\. COMMISSION is a DECIMAL column\. The scale of the result is reduced to 10 digits\. 

```
select cast(stddev(commission) as dec(18,10))
from sales;

stddev
----------------
130.3912659086
(1 row)
```

The following query casts the sample standard deviation for the COMMISSION column as an integer\. 

```
select cast(stddev(commission) as integer)
from sales;

stddev
--------
130
(1 row)
```

The following query returns both the sample standard deviation and the square root of the sample variance for the COMMISSION column\. The results of these calculations are the same\. 

```
select
cast(stddev_samp(commission) as dec(18,10)) stddevsamp,
cast(sqrt(var_samp(commission)) as dec(18,10)) sqrtvarsamp
from sales;

stddevsamp   |  sqrtvarsamp
----------------+----------------
130.3912659086 | 130.3912659086
(1 row)
```