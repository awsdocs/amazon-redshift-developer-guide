# ST\_Force2D<a name="ST_Force2D-function"></a>

ST\_Force2D returns a 2D geometry of the input geometry\. For 2D geometries, a copy of the input is returned\. For 3DZ, 3DM, and 4D geometries, ST\_Force2D projects the geometry to the xy\-Cartesian plane\. Empty points in the input geometry remain empty points in the output geometry\.

## Syntax<a name="ST_Force2D-function-syntax"></a>

```
ST_Force2D(geom)
```

## Arguments<a name="ST_Force2D-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Force2D-function-return"></a>

`GEOMETRY`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

If *geom* is empty, then an empty geometry is returned\. 

## Examples<a name="ST_Force2D-function-examples"></a>

The following SQL returns a 2D geometry from a 3DZ geometry\. 

```
SELECT ST_AsEWKT(ST_Force2D(ST_GeomFromText('MULTIPOINT Z(0 1 2, EMPTY, 2 3 4, 5 6 7)')));
```

```
st_asewkt
-----------
  MULTIPOINT((0 1),EMPTY,(2 3),(5 6))
```