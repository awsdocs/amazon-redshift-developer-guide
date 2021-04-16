# CBRT function<a name="r_CBRT"></a>

 The CBRT function is a mathematical function that calculates the cube root of a number\. 

## Syntax<a name="r_CBRT-synopsis"></a>

```
CBRT (number)
```

## Argument<a name="r_CBRT-argument"></a>

CBRT takes a DOUBLE PRECISION number as an argument\. 

## Return type<a name="r_CBRT-return-type"></a>

CBRT returns a DOUBLE PRECISION number\. 

## Examples<a name="r_CBRT-examples"></a>

Calculate the cube root of the commission paid for a given transaction: 

```
select cbrt(commission) from sales where salesid=10000;

cbrt
------------------
3.03839539048843
(1 row)
```