# ST\_Contains<a name="ST_Contains-function"></a>

ST\_Contains returns true if the first input geometry contains the second input geometry\. Geometry `A` contains geometry `B` if every point in `B` is a point in `A`, and their interiors have nonempty intersection\. 

ST\_Contains\(`A`, `B`\) is equivalent to ST\_Within\(`B`, `A`\)\. 

## Syntax<a name="ST_Contains-function-syntax"></a>

```
ST_Contains(geom1, geom2)
```

## Arguments<a name="ST_Contains-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. This value is compared with *geom1* to determine if it is contained within *geom1*\. 

## Return type<a name="ST_Contains-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Contains-function-examples"></a>

The following SQL checks if the first polygon contains the second polygon\. 

```
SELECT ST_Contains(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'));
```

```
st_contains
-----------
 false
```