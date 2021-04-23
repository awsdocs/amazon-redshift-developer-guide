# ST\_Points<a name="ST_Points-function"></a>

ST\_Points returns a multipoint geometry containing all nonempty points in the input geometry\. ST\_Points doesn't remove points that are duplicated in the input, including the start and end points of ring geometries\.

## Syntax<a name="ST_Points-function-syntax"></a>

```
ST_Points(geom)
```

## Arguments<a name="ST_Points-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Points-function-return"></a>

`GEOMETRY` of subtype `MULTIPOINT`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the same as *geom*\. 

If *geom* is null, then null is returned\. 

If *geom* has zero nonempty points, then an empty multipoint is returned\. 

## Examples<a name="ST_Points-function-examples"></a>

The following SQL examples construct a multipoint geometry from the input geometry\. The result is a multipoint geometry containing the nonempty points in the input geometry\.

```
SELECT ST_AsEWKT(ST_Points(ST_SetSRID(ST_GeomFromText('LINESTRING(1 0,2 0,3 0)'), 4326)));
```

```
st_asewkt
-------------
SRID=4326;MULTIPOINT((1 0),(2 0),(3 0))
```

```
SELECT ST_AsEWKT(ST_Points(ST_SetSRID(ST_GeomFromText('MULTIPOLYGON(((0 0,1 0,0 1,0 0)))'), 4326)));
```

```
st_asewkt
-------------
SRID=4326;MULTIPOINT((0 0),(1 0),(0 1),(0 0))
```