# ST\_Force4D<a name="ST_Force4D-function"></a>

ST\_Force4D returns a 4D geometry of the input geometry\. For 2D geometries, the `z` and `m` coordinates of the nonempty points in the output geometry are all set to `0`\. For 3DM geometries, the `z` coordinates of the nonempty points in the output geometry are all set to `0`\. For 3DZ geometries, the `m` coordinates of the nonempty points in the output geometry are all set to `0`\. For 4D geometries, a copy of the input geometry is returned\. Empty points in the input geometry remain empty points in the output geometry\.

## Syntax<a name="ST_Force4D-function-syntax"></a>

```
ST_Force4D(geom)
```

## Arguments<a name="ST_Force4D-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Force4D-function-return"></a>

`GEOMETRY`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

If *geom* is empty, then an empty geometry is returned\. 

## Examples<a name="ST_Force4D-function-examples"></a>

The following SQL returns a 4D geometry from a 3DM geometry\. 

```
SELECT ST_AsEWKT(ST_Force4D(ST_GeomFromText('MULTIPOINT M(0 1 2, EMPTY, 2 3 4, 5 6 7)')));
```

```
st_asewkt
-----------
  MULTIPOINT ZM ((0 1 0 2),EMPTY,(2 3 0 4),(5 6 0 7))
```