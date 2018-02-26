# SIGN Function<a name="r_SIGN"></a>

 The SIGN function returns the sign \(positive or negative\) of a numeric value\. The result of the SIGN function will be a `1`, `-1`, or `0` indicating the sign of the argument\. 

## Syntax<a name="r_SIGN-synopsis"></a>

```
SIGN (numeric)
```

## Argument<a name="r_SIGN-argument"></a>

 *numeric*   
Numeric value to be evaluated\. 

## Return Type<a name="r_SIGN-return-type"></a>

Integer

## Examples<a name="r_SIGN-examples"></a>

Determine the sign of the commission paid for a given transaction: 

```
select commission, sign (commission)
from sales where salesid=10000;

commission | sign
-----------+------
     28.05 |    1
(1 row)
```