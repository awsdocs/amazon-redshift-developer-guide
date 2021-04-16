# SIN function<a name="r_SIN"></a>

SIN is a trigonometric function that returns the sine of a number\. The return value is between `-1` and `1`\. 

## Syntax<a name="r_SIN-synopsis"></a>

```
SIN(number)
```

## Argument<a name="r_SIN-argument"></a>

 *number*   
A double precision number in radians\. 

## Return type<a name="r_SIN-return-type"></a>

The SIN function returns a double precision number\. 

## Examples<a name="r_SIN-examples"></a>

The following example returns the sine of `-PI`: 

```
select sin(-pi());

sin
-----------------------
-1.22464679914735e-16
(1 row)
```