# ST\_Within<a name="ST_Within-function"></a>

ST\_Within returns true if the first input geometry is within the second input geometry\. 

For example, geometry `A` is within geometry `B` if every point in `A` is a point in `B` and their interiors have nonempty intersection\. 

ST\_Within\(`A`, `B`\) is equivalent to ST\_Contains\(`B`, `A`\)\. 

## Syntax<a name="ST_Within-function-syntax"></a>

```
ST_Within(geom1, geom2)
```

## Arguments<a name="ST_Within-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. This value is compared with *geom2* to determine if it is within *geom2*\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Within-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same spatial reference system identifier \(SRID\) value, then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Within-function-examples"></a>

The following SQL checks if the first polygon is within the second polygon\. 

```
SELECT ST_Within(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'));
```

```
st_within
-----------
 true
```