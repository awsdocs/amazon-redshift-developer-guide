# ST\_Dimension<a name="ST_Dimension-function"></a>

ST\_Dimension returns the inherent dimension of an input geometry\. The *inherent dimension* is the dimension value of the subtype that is defined in the geometry\. 

## Syntax<a name="ST_Dimension-function-syntax"></a>

```
ST_Dimension(geom)
```

## Arguments<a name="ST_Dimension-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Dimension-function-return"></a>

`INTEGER` representing the inherent dimension of *geom*\. 

If *geom* is null, then null is returned\. 

The values returned are as follows\.


| Returned value | Geometry subtype | 
| --- | --- | 
| 0 | Returned if *geom* is a `POINT` or `MULTIPOINT` subtype | 
| 1 | Returned if *geom* is a `LINESTRING` or `MULTILINESTRING` subtype\. | 
| 2 | Returned if *geom* is a `POLYGON` or `MULTIPOLYGON` subtype | 
| 0 | Returned if *geom* is an empty `GEOMETRYCOLLECTION` subtype | 
| Largest dimension of the components of the collection | Returned if *geom* is a `GEOMETRYCOLLECTION` subtype | 

## Examples<a name="ST_Dimension-function-examples"></a>

The following SQL converts a well\-known text \(WKT\) representation of a four\-point LINESTRING to a GEOMETRY object and returns the dimension of the linestring\. 

```
SELECT ST_Dimension(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_dimension
-------------
1
```