# ST\_LineInterpolatePoint<a name="ST_LineInterpolatePoint-function"></a>

ST\_LineInterpolatePoint returns a point along a line at a fractional distance from the start of the line\. 

To determine point equality, ST\_LineInterpolatePoint operates on the 2D projection of the input geometry\. If the input geometry is empty, a copy of it is returned in the same dimension as the input\. For 3DZ, 3DM, and 4D geometries, the `z` or `m` coordinate is the average of the `z` or `m` coordinates of the segment where the point lies\.

## Syntax<a name="ST_LineInterpolatePoint-function-syntax"></a>

```
ST_LineInterpolatePoint(geom, fraction)
```

## Arguments<a name="ST_LineInterpolatePoint-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype is `LINESTRING`\. 

 *fraction*   
A value of data type `DOUBLE PRECISION` that represents the position of a point along the linestring for the line\. The value is a fraction in the range 0–1, inclusive\. 

## Return type<a name="ST_LineInterpolatePoint-function-return"></a>

`GEOMETRY` of subtype `POINT`\. 

If *geom* or *fraction* is null, then null is returned\. 

If *geom* is empty, then the empty point is returned\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *fraction* is out of range, then an error is returned\. 

If *geom* is not a linestring, then an error is returned\. 

## Examples<a name="ST_LineInterpolatePoint-function-examples"></a>

The following SQL returns a point halfway along a linestring\.

```
SELECT ST_AsEWKT(ST_LineInterpolatePoint(ST_GeomFromText('LINESTRING(0 0, 5 5, 7 7, 10 10)'), 0.50));
```

```
st_asewkt
-----------
 POINT(5 5)
```

The following SQL returns a point 90 percent of the way along a linestring\.

```
SELECT ST_AsEWKT(ST_LineInterpolatePoint(ST_GeomFromText('LINESTRING(0 0, 5 5, 7 7, 10 10)'), 0.90));
```

```
st_asewkt
-----------
 POINT(9 9)
```