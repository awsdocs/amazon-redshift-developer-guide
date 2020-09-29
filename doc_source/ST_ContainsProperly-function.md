# ST\_ContainsProperly<a name="ST_ContainsProperly-function"></a>

ST\_ContainsProperly returns true if both input geometries are nonempty, and all points of the second geometry are interior points of the first geometry\. 

## Syntax<a name="ST_ContainsProperly-function-syntax"></a>

```
ST_ContainsProperly(geom1, geom2)
```

## Arguments<a name="ST_ContainsProperly-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype can't be `GEOMETRYCOLLECTION`\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype can't be `GEOMETRYCOLLECTION`\. This value is compared with *geom1* to determine if all its points are interior points of *geom1*\. 

## Return type<a name="ST_ContainsProperly-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_ContainsProperly-function-examples"></a>

The following SQL returns the values of ST\_Contains and ST\_ContainsProperly where the input linestring intersects the interior and the boundary of the input polygon \(but not its exterior\)\. The polygon contains the linestring but doesn't properly contain the linestring\. 

```
WITH tmp(g1, g2) 
AS (SELECT ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0))'), ST_GeomFromText('LINESTRING(5 5,10 5,10 6,5 5)')) SELECT ST_Contains(g1, g2), ST_ContainsProperly(g1, g2) 
FROM tmp;
```

```
 st_contains | st_containsproperly 
-------------+---------------------
 t           | f
```