# ST\_ConvexHull<a name="ST_ConvexHull-function"></a>

ST\_ConvexHull returns a geometry that represents the convex hull of the nonempty points contained in the input geometry\. 

## Syntax<a name="ST_ConvexHull-function-syntax"></a>

```
ST_ConvexHull(geom)
```

## Arguments<a name="ST_ConvexHull-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_ConvexHull-function-return"></a>

`GEOMETRY`

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

The values returned are as follows\.


| Number of points on the convex hull | Geometry subtype | 
| --- | --- | 
| 0 | A copy of *geom* is returned\.  | 
| 1 | A `POINT` subtype is returned\.  | 
| 2 | A `LINESTRING` subtype is returned\. The two points of the returned linestring are lexicographically ordered\. | 
| 3 or greater | A `POLYGON` subtype with no interior rings is returned\. The polygon is clockwise oriented, and the first point of the exterior ring is the lexicographically smallest point of the ring\. | 

## Examples<a name="ST_ConvexHull-function-examples"></a>

The following SQL returns the extended well\-known text \(EWKT\) representation of a linestring\. In this case, the convex hull returned is a polygon\.

```
SELECT ST_AsEWKT(ST_ConvexHull(ST_GeomFromText('LINESTRING(0 0,1 0,0 1,1 1,0.5 0.5)'))) as output;
```

```
output
-------------
POLYGON((0 0,0 1,1 1,1 0,0 0))
```

The following SQL returns the EWKT representation of a linestring\. In this case, the convex hull returned is a linestring\.

```
SELECT ST_AsEWKT(ST_ConvexHull(ST_GeomFromText('LINESTRING(0 0,1 1,0.2 0.2,0.6 0.6,0.5 0.5)'))) as output;
```

```
output
-------------
LINESTRING(0 0,1 1)
```

The following SQL returns the EWKT representation of a multipoint\. In this case, the convex hull returned is a point\.

```
SELECT ST_AsEWKT(ST_ConvexHull(ST_GeomFromText('MULTIPOINT(0 0,0 0,0 0)'))) as output;
```

```
output
-------------
POINT(0 0)
```