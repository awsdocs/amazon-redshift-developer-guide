# ST\_Force3DZ<a name="ST_Force3DZ-function"></a>

ST\_Force3DZ returns a 3DZ geometry from the input geometry\. For 2D geometries, the `z` coordinates of the nonempty points in the output geometry are all set to `0`\. For 3DM geometries, the geometry is projected on the xy\-Cartesian plane, and the `z` coordinates of the nonempty points in the output geometry are all set to `0`\. For 3DZ geometries, a copy of the input geometry is returned\. For 4D geometries, the geometry is projected to the xyz\-Cartesian space\. Empty points in the input geometry remain empty points in the output geometry\.

## Syntax<a name="ST_Force3DZ-function-syntax"></a>

```
ST_Force3DZ(geom)
```

## Arguments<a name="ST_Force3DZ-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Force3DZ-function-return"></a>

`GEOMETRY`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

If *geom* is empty, then an empty geometry is returned\. 

## Examples<a name="ST_Force3DZ-function-examples"></a>

The following SQL returns a 3DZ geometry from a 3DM geometry\. 

```
SELECT ST_AsEWKT(ST_Force3DZ(ST_GeomFromText('MULTIPOINT M(0 1 2, EMPTY, 2 3 4, 5 6 7)')));
```

```
st_asewkt
-----------
  MULTIPOINT Z ((0 1 0),EMPTY,(2 3 0),(5 6 0))
```