# VAR\_SAMP and VAR\_POP functions<a name="r_VARIANCE_functions"></a>

 The VAR\_SAMP and VAR\_POP functions return the sample and population variance of a set of numeric values \(integer, decimal, or floating\-point\)\. The result of the VAR\_SAMP function is equivalent to the squared sample standard deviation of the same set of values\. 

VAR\_SAMP and VARIANCE are synonyms for the same function\. 

## Syntax<a name="r_VARIANCE_functions-syntax"></a>

```
VAR_SAMP | VARIANCE ( [ DISTINCT | ALL ] expression)
VAR_POP ( [ DISTINCT | ALL ] expression)
```

The expression must have an integer, decimal, or floating\-point data type\. Regardless of the data type of the expression, the return type of this function is a double precision number\. 

**Note**  
The results of these functions might vary across data warehouse clusters, depending on the configuration of the cluster in each case\. 

## Usage notes<a name="r_VARIANCE_usage_notes"></a>

When the sample variance \(VARIANCE or VAR\_SAMP\) is calculated for an expression that consists of a single value, the result of the function is NULL not 0\. 

## Examples<a name="r_VARIANCE_functions-examples"></a>

The following query returns the rounded sample and population variance of the NUMTICKETS column in the LISTING table\. 

```
select avg(numtickets),
round(var_samp(numtickets)) varsamp,
round(var_pop(numtickets)) varpop
from listing;

avg | varsamp | varpop
-----+---------+--------
10 |      54 |     54
(1 row)
```

The following query runs the same calculations but casts the results to decimal values\. 

```
select avg(numtickets),
cast(var_samp(numtickets) as dec(10,4)) varsamp,
cast(var_pop(numtickets) as dec(10,4)) varpop
from listing;

avg | varsamp | varpop
-----+---------+---------
10 | 53.6291 | 53.6288
(1 row)
```