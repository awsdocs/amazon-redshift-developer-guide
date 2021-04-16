# ATAN function<a name="r_ATAN"></a>

ATAN is a trigonometric function that returns the arc tangent of a number\. The return value is in radians and is between PI/2 and \-PI/2\. 

## Syntax<a name="r_ATAN-synopsis"></a>

```
ATAN(number)
```

## Argument<a name="r_ATAN-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_ATAN-return-type"></a>

The ATAN function returns a double precision number\. 

## Examples<a name="r_ATAN-examples"></a>

The following example returns the arc tangent of 1 and multiplies it by 4: 

```
select atan(1) * 4 as pi;
pi
------------------
3.14159265358979
(1 row)
```

The following example converts the arc tangent of 1 to the equivalent number of degrees: 

```
select (atan(1) * 180/(select pi())) as degrees;
degrees
---------
45
(1 row)
```