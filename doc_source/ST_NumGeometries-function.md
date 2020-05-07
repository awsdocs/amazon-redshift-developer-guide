# ST\_NumGeometries<a name="ST_NumGeometries-function"></a>

ST\_NumGeometries returns the number of geometries in an input geometry\. 

## Syntax<a name="ST_NumGeometries-function-syntax"></a>

```
ST_NumGeometries(geom)
```

## Arguments<a name="ST_NumGeometries-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_NumGeometries-function-return"></a>

`INTEGER` representing the number of geometries in *geom*\. 

If *geom* is `GEOMETRYCOLLECTION` or a `MULTI` subtype, then the number of geometries is returned\. 

If *geom* is a single geometry, then `1` is returned\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_NumGeometries-function-examples"></a>

The following SQL returns the number of geometries in the input multilinestring\. 

```
SELECT ST_NumGeometries(ST_GeomFromText('MULTILINESTRING((0 0,1 0,0 5),(3 4,13 26))'));
```

```
st_numgeometries
-------------
 2
```