# ST\_CoveredBy<a name="ST_CoveredBy-function"></a>

ST\_CoveredBy returns true if the first input geometry is covered by the second input geometry\. Geometry `A` is covered by geometry `B` if both are nonempty and every point in `A` is a point in `B`\. 

ST\_CoveredBy\(`A`, `B`\) is equivalent to ST\_Covers\(`B`, `A`\)\. 

## Syntax<a name="ST_CoveredBy-function-syntax"></a>

```
ST_CoveredBy(geom1, geom2)
```

## Arguments<a name="ST_CoveredBy-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. This value is compared with *geom2* to determine if it's covered by *geom2*\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_CoveredBy-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_CoveredBy-function-examples"></a>

The following SQL checks if the first polygon is covered by the second polygon\. 

```
SELECT ST_CoveredBy(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'));
```

```
st_coveredby
-----------
 true
```