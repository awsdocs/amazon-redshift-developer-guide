# ROUND function<a name="r_ROUND"></a>

The ROUND function rounds numbers to the nearest integer or decimal\. 

The ROUND function can optionally include a second argument as an integer to indicate the number of decimal places for rounding, in either direction\. When you don't provide the second argument, the function rounds to the nearest whole number\. When the second argument *>n* is specified, the function rounds to the nearest number with *n* decimal places of precision\. 

## Syntax<a name="r_ROUND-synopsis"></a>

```
ROUND (number [ , integer ] )
```

## Argument<a name="r_ROUND-argument"></a>

 *number*   
A number or expression that evaluates to a number\. It can be the DECIMAL, FLOAT8 or SUPER type\. Amazon Redshift can convert other data types per the implicit conversion rules\. 

*integer* \(optional\)  
An integer that indicates the number of decimal places for rounding in either directions\. The extra argument isn't supported for the SUPER data type\.

## Return type<a name="r_ROUND-return-type"></a>

ROUND returns the same numeric data type as the input argument\(s\)\. 

When the input is of the SUPER type, the output retains the same dynamic type as the input while the static type remains the SUPER type\. When the dynamic type of SUPER isn't a number, Amazon Redshift returns a null\.

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