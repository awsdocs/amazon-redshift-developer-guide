# ROUND function<a name="r_ROUND"></a>

The ROUND function rounds numbers to the nearest integer or decimal\. 

The ROUND function can optionally include a second argument: an integer to indicate the number of decimal places for rounding, in either direction\. If the second argument is not provided, the function rounds to the nearest whole number; if the second argument *n* is specified, the function rounds to the nearest number with *n* decimal places of precision\. 

## Syntax<a name="r_ROUND-synopsis"></a>

```
ROUND (number [ , integer ] )
```

## Argument<a name="r_ROUND-argument"></a>

 *number*   
INTEGER, DECIMAL, and FLOAT data types are supported\.   
If the first argument is an integer, the parser converts the integer into a decimal data type prior to processing\. If the first argument is a decimal number, the parser processes the function without conversion, resulting in better performance\. 

## Return type<a name="r_ROUND-return-type"></a>

ROUND returns the same numeric data type as the input argument\(s\)\. 

## Examples<a name="r_ROUND-examples"></a>

Round the commission paid for a given transaction to the nearest whole number\. 

```
select commission, round(commission)
from sales where salesid=10000;

commission | round
-----------+-------
     28.05 |    28
(1 row)
```

Round the commission paid for a given transaction to the first decimal place\. 

```
select commission, round(commission, 1)
from sales where salesid=10000;

commission | round
-----------+-------
     28.05 |  28.1
(1 row)
```

For the same query, extend the precision in the opposite direction\. 

```
select commission, round(commission, -1)
from sales where salesid=10000;

commission | round
-----------+-------
     28.05 |    30
(1 row)
```