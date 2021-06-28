# ST\_MMax<a name="ST_MMax-function"></a>

ST\_MMax returns the maximum `m` coordinate of an input geometry\. 

## Syntax<a name="ST_MMax-function-syntax"></a>

```
ST_MMax(geom)
```

## Arguments<a name="ST_MMax-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_MMax-function-return"></a>

`DOUBLE PRECISION` value of the maximum `m` coordinate\.

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

If *geom* is a 2D or 3DZ geometry, then null is returned\. 

## Examples<a name="ST_MMax-function-examples"></a>

The following SQL returns the largest `m` coordinate of a linestring in a 3DM geometry\. 

```
SELECT ST_MMax(ST_GeomFromEWKT('LINESTRING M (0 1 2, 3 4 5, 6 7 8)'));
```

```
st_mmax
-----------
  8
```

The following SQL returns the largest `m` coordinate of a linestring in a 4D geometry\. 

```
SELECT ST_MMax(ST_GeomFromEWKT('LINESTRING ZM (0 1 2 3, 4 5 6 7, 8 9 10 11)'));
```

```
st_mmax
-----------
  11
```