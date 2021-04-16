# ST\_GeometryType<a name="ST_GeometryType-function"></a>

ST\_GeometryType returns the subtype of an input geometry as a string\. 

## Syntax<a name="ST_GeometryType-function-syntax"></a>

```
ST_GeometryType(geom)
```

## Arguments<a name="ST_GeometryType-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_GeometryType-function-return"></a>

`VARCHAR` representing the subtype of *geom*\. 

If *geom* is null, then null is returned\. 

The values returned are as follows\.


| Returned string value | Geometry subtype | 
| --- | --- | 
| `ST_Point` | Returned if *geom* is a `POINT` subtype  | 
| `ST_LineString` | Returned if *geom* is a `LINESTRING` subtype  | 
| `ST_Polygon` | Returned if *geom* is a `POLYGON` subtype  | 
| `ST_MultiPoint` | Returned if *geom* is a `MULTIPOINT` subtype  | 
| `ST_MultiLineString` | Returned if *geom* is a `MULTILINESTRING` subtype  | 
| `ST_MultiPolygon` | Returned if *geom* is a `MULTIPOLYGON` subtype  | 
| `ST_GeometryCollection` | Returned if *geom* is a `GEOMETRYCOLLECTION` subtype  | 

## Examples<a name="ST_GeometryType-function-examples"></a>

The following SQL returns the subtype of the input linestring geometry\. 

```
SELECT ST_GeometryType(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_geometrytype
-------------
 ST_LineString
```