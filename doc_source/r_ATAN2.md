# ATAN2 function<a name="r_ATAN2"></a>

ATAN2 is a trigonometric function that returns the arc tangent of a one number divided by another number\. The return value is in radians and is between PI/2 and \-PI/2\. 

## Syntax<a name="r_ATAN2-synopsis"></a>

```
ATAN2(number1, number2)
```

## Arguments<a name="r_ATAN2-arguments"></a>

 *number1*   
The first input parameter is a double precision number\. 

 *number2*   
The second parameter is a double precision number\. 

## Return type<a name="r_ATAN2-return-type"></a>

The ATAN2 function returns a double precision number\. 

## Examples<a name="r_ATAN2-examples"></a>

The following example returns the arc tangent of 2/2 and multiplies it by 4: 

```
select atan2(2,2) * 4 as pi;
pi
------------------
3.14159265358979
(1 row)
```

The following example converts the arc tangent of 1/0 \(or 0\) to the equivalent number of degrees: 

```
select (atan2(1,0) * 180/(select pi())) as degrees;
degrees
---------
90
(1 row)
```