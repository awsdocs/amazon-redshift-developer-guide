# ACOS function<a name="r_ACOS"></a>

ACOS is a trigonometric function that returns the arc cosine of a number\. The return value is in radians and is between PI/2 and \-PI/2\. 

## Syntax<a name="r_ACOS-synopsis"></a>

```
ACOS(number)
```

## Arguments<a name="r_ACOS-arguments"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_ACOS-return-type"></a>

The ACOS function returns a double precision number\. 

## Examples<a name="r_ACOS-examples"></a>

The following example returns the arc cosine of \-1: 

```
select acos(-1);
acos
------------------
3.14159265358979
(1 row)
```

The following example converts the arc cosine of \.5 to the equivalent number of degrees: 

```
select (acos(.5) * 180/(select pi())) as degrees;
degrees
---------
60
(1 row)
```