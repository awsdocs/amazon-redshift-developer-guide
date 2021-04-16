# ST\_GeometryN<a name="ST_GeometryN-function"></a>

ST\_GeometryN returns a geometry pointed to by the input index of the input geometry, as follows: 
+ If the input is a point, linestring, or polygon, then a geometry is returned as is if the index is equal to one \(1\), and null if the index is other than one \(1\)\.
+ If the input is a multipoint, multilinestring, multipolygon, or geometry collection, then a point, linestring, polygon, or geometry collection is returned as pointed to by an input index\. 

The index is one\-based\. The spatial reference system identifier \(SRID\) of the result is the same as that of the input geometry\. 

## Syntax<a name="ST_GeometryN-function-syntax"></a>

```
ST_GeometryN(geom, index)
```

## Arguments<a name="ST_GeometryN-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.  

 *index*   
A value of data type `INTEGER` that represents the position of a one\-based index\. 

## Return type<a name="ST_GeometryN-function-return"></a>

`GEOMETRY` 

If *geom* or *index* is null, then null is returned\. 

If *index* is out of range, then an error is returned\. 

## Examples<a name="ST_GeometryN-function-examples"></a>

The following SQL returns the geometries in a geometry collection\. 

```
WITH tmp1(idx) AS (SELECT 1 UNION SELECT 2),
tmp2(g) AS (SELECT ST_GeomFromText('GEOMETRYCOLLECTION(POLYGON((0 0,10 0,0 10,0 0)),LINESTRING(20 10,20 0,10 0))'))
SELECT idx, ST_AsEWKT(ST_GeometryN(g, idx)) FROM tmp1, tmp2 ORDER BY idx;
```

```
 idx |          st_asewkt           
-----+------------------------------
   1 | POLYGON((0 0,10 0,0 10,0 0))
   2 | LINESTRING(20 10,20 0,10 0)
```