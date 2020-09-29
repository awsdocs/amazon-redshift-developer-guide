# ST\_IsPolygonCW<a name="ST_IsPolygonCW-function"></a>

ST\_IsPolygonCW returns true if the input polygon or multipolygon is clockwise\. If the input geometry is a point, linestring, multipoint, or multilinestring, then true is returned\. For geometry collections, ST\_IsPolygonCW returns true if all the geometries in the collection are clockwise\. 

## Syntax<a name="ST_IsPolygonCW-function-syntax"></a>

```
ST_IsPolygonCW(geom)
```

## Arguments<a name="ST_IsPolygonCW-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_IsPolygonCW-function-return"></a>

`BOOLEAN`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_IsPolygonCW-function-examples"></a>

The following SQL checks if the polygon is clockwise\. 

```
SELECT ST_IsPolygonCW(ST_GeomFromText('POLYGON((7 9,8 7,11 6,15 8,16 6,17 7,17 10,18 12,17 14,15 15,11 15,10 13,9 12,7 9),(9 9,10 10,11 11,11 10,10 8,9 9),(12 14,15 14,13 11,12 14))'));
```

```
 st_ispolygonccw
----------
 true
```