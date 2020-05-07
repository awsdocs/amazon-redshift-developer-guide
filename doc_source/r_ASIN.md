# ASIN function<a name="r_ASIN"></a>

ASIN is a trigonometric function that returns the arc sine of a number\. The return value is in radians and is between PI/2 and \-PI/2\. 

## Syntax<a name="r_ASIN-synopsis"></a>

```
ASIN(number)
```

## Argument<a name="r_ASIN-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_ASIN-return-type"></a>

The ASIN function returns a double precision number\. 

## Examples<a name="r_ASIN-examples"></a>

The following example returns the arc sine of 1 and multiples it by 2: 

```
select asin(1)*2 as pi;
pi
------------------
3.14159265358979
(1 row)
```

The following example converts the arc sine of \.5 to the equivalent number of degrees: 

```
select (asin(.5) * 180/(select pi())) as degrees;
degrees
---------
30
(1 row)
```