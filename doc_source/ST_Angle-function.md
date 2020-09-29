# ST\_Angle<a name="ST_Angle-function"></a>

ST\_Angle returns the angle in radians between points measured clockwise as follows:
+ If three points are input, then the returned angle P1\-P2\-P3 is measured as if the angle was obtained by rotating from P1 to P3 around P2 clockwise\. 
+ If four points are input, then the returned clockwise angle formed by the directed lines P1\-P2 and P3\-P4 is returned\. If the input is a degenerate case \(that is, P1 equals P2, or P3 equals P4\), then null is returned\. 

The return value is in radians and in the range \[0, 2π\)\. 

## Syntax<a name="ST_Angle-function-syntax"></a>

```
ST_Angle(geom1, geom2, geom3)
```

```
ST_Angle(geom1, geom2, geom3, geom4)
```

## Arguments<a name="ST_Angle-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

 *geom3*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

 *geom4*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

## Return type<a name="ST_Angle-function-return"></a>

`DOUBLE PRECISION`\. 

If *geom1* equals *geom2*, or *geom2* equals *geom3*, then a null is returned\. 

If *geom1*, *geom2*, *geom3*, or *geom4* is null, then a null is returned\. 

If *geom1*, *geom2*, *geom3*, *geom4* are not two\-dimensional points, then an error is returned\. 

If *geom1*, *geom2*, *geom3*, *geom4* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

## Examples<a name="ST_Angle-function-examples"></a>

The following SQL returns the angle converted to degrees of three input points\. 

```
SELECT ST_Angle(ST_Point(1,1), ST_Point(0,0), ST_Point(1,0)) / Pi() * 180.0 AS angle;
```

```
 angle
---------------
    45
```

The following SQL returns the angle converted to degrees of four input points\. 

```
SELECT ST_Angle(ST_Point(1,1), ST_Point(0,0), ST_Point(1,0), ST_Point(2,0)) / Pi() * 180.0 AS angle;
```

```
 angle
---------------
   225
```