# ST\_Azimuth<a name="ST_Azimuth-function"></a>

ST\_Azimuth returns the north\-based Cartesian azimuth of two input points\. 

## Syntax<a name="ST_Azimuth-function-syntax"></a>

```
ST_Azimuth(point1, point2)
```

## Arguments<a name="ST_Azimuth-function-arguments"></a>

 *point1*   
A `POINT` value of data type `GEOMETRY`\. The spatial reference system identifier \(SRID\) of *point1* must match the SRID of *point2*\. 

 *point2*   
A `POINT` value of data type `GEOMETRY`\. The SRID of *point2* must match the SRID of *point1*\. 

## Return type<a name="ST_Azimuth-function-return"></a>

A number that is an angle in radians of `DOUBLE PRECISION` data type\. Values range from 0 \(inclusive\) to 2 pi \(exclusive\)\. 

If either *point1* or *point2* is null, then null is returned\. 

If *point1* and *point2* are equal, then null is returned\. 

If *point1* or *point2* is not a point, then an error is returned\. 

If *point1* and *point2* don't have the value for the spatial reference system identifier \(SRID\), then an error is returned\. 

## Examples<a name="ST_Azimuth-function-examples"></a>

The following SQL returns the azimuth of the input points\. 

```
SELECT ST_Azimuth(ST_Point(1,2), ST_Point(5,6));
```

```
st_azimuth
-------------------
 0.7853981633974483
```