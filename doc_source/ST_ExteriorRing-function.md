# ST\_ExteriorRing<a name="ST_ExteriorRing-function"></a>

ST\_ExteriorRing returns a closed linestring that represents the exterior ring of an input polygon\. 

## Syntax<a name="ST_ExteriorRing-function-syntax"></a>

```
ST_ExteriorRing(geom)
```

## Arguments<a name="ST_ExteriorRing-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_ExteriorRing-function-return"></a>

`GEOMETRY` of subtype `LINESTRING`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

If *geom* is not a polygon, then null is returned\. 

If *geom* is empty, then an empty polygon is returned\. 

## Examples<a name="ST_ExteriorRing-function-examples"></a>

The following SQL returns the exterior ring of a polygon as a closed linestring\. 

```
SELECT ST_AsEWKT(ST_ExteriorRing(ST_GeomFromText('POLYGON((7 9,8 7,11 6,15 8,16 6,17 7,17 10,18 12,17 14,15 15,11 15,10 13,9 12,7 9),(9 9,10 10,11 11,11 10,10 8,9 9),(12 14,15 14,13 11,12 14))')));
```

```
st_asewkt
-----------
 LINESTRING(7 9,8 7,11 6,15 8,16 6,17 7,17 10,18 12,17 14,15 15,11 15,10 13,9 12,7 9)
```