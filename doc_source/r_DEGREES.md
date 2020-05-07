# DEGREES function<a name="r_DEGREES"></a>

Converts an angle in radians to its equivalent in degrees\. 

## Syntax<a name="r_DEGREES-synopsis"></a>

```
DEGREES(number)
```

## Argument<a name="r_DEGREES-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_DEGREES-return-type"></a>

The DEGREES function returns a double precision number\. 

## Examples<a name="r_DEGREES-examples"></a>

The following example returns the degree equivalent of \.5 radians: 

```
select degrees(.5);
degrees
------------------
28.6478897565412
(1 row)
```

The following example converts PI radians to degrees: 

```
select degrees(pi());
degrees
---------
180
(1 row)
```