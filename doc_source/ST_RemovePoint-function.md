# ST\_RemovePoint<a name="ST_RemovePoint-function"></a>

ST\_RemovePoint returns a linestring geometry that has the point of the input geometry at an index position removed\. 

The index is zero\-based\. The spatial reference system identifier \(SRID\) of the result is the same as the input geometry\. 

## Syntax<a name="ST_RemovePoint-function-syntax"></a>

```
ST_RemovePoint(geom, index)
```

## Arguments<a name="ST_RemovePoint-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. 

 *index*   
A value of data type `INTEGER` that represents the position of a zero\-based index\. 

## Return type<a name="ST_RemovePoint-function-return"></a>

`GEOMETRY` 

If *geom* or *index* is null, then null is returned\. 

If *geom* is not subtype `LINESTRING`, then an error is returned\. 

If *index* is out of range, then an error is returned\. Valid values for the index position are between 0 and `ST_NumPoints(geom)` minus 1\. 

## Examples<a name="ST_RemovePoint-function-examples"></a>

The following SQL removes the last point in a linestring\. 

```
WITH tmp(g) AS (SELECT ST_GeomFromText('LINESTRING(0 0,10 0,10 10,5 5,0 5)',4326))
SELECT ST_AsEWKT(ST_RemovePoint(g, ST_NumPoints(g) - 1)) FROM tmp;
```

```
   st_asewkt
-----------------------------------------
 SRID=4326;LINESTRING(0 0,10 0,10 10,5 5)
```