# ST\_ZMax<a name="ST_ZMax-function"></a>

ST\_ZMax returns the maximum `z` coordinate of an input geometry\. 

## Syntax<a name="ST_ZMax-function-syntax"></a>

```
ST_ZMax(geom)
```

## Arguments<a name="ST_ZMax-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_ZMax-function-return"></a>

`DOUBLE PRECISION` value of the maximum `z` coordinate\.

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

If *geom* is a 2D or 3DM geometry, then null is returned\. 

## Examples<a name="ST_ZMax-function-examples"></a>

The following SQL returns the largest `z` coordinate of a linestring in a 3DZ geometry\. 

```
SELECT ST_ZMax(ST_GeomFromEWKT('LINESTRING Z (0 1 2, 3 4 5, 6 7 8)'));
```

```
st_zmax
-----------
  8
```

The following SQL returns the largest `z` coordinate of a linestring in a 4D geometry\. 

```
SELECT ST_ZMax(ST_GeomFromEWKT('LINESTRING ZM (0 1 2 3, 4 5 6 7, 8 9 10 11)'));
```

```
st_zmax
-----------
  10
```