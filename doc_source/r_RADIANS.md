# RADIANS function<a name="r_RADIANS"></a>

Converts an angle in degrees to its equivalent in radians\. 

## Syntax<a name="r_RADIANS-synopsis"></a>

```
RADIANS(number)
```

## Argument<a name="r_RADIANS-argument"></a>

 *string*   
The input parameter is a double precision number\. 

## Return type<a name="r_RADIANS-return-type"></a>

The RADIANS function returns a double precision number\. 

## Examples<a name="r_RADIANS-examples"></a>

The following example returns the radian equivalent of 180 degrees: 

```
select radians(180);
radians
------------------
3.14159265358979
(1 row)
```