# ST\_XMax<a name="ST_XMax-function"></a>

ST\_XMax returns the maximum first coordinate of an input geometry\. 

## Syntax<a name="ST_XMax-function-syntax"></a>

```
ST_XMax(geom)
```

## Arguments<a name="ST_XMax-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_XMax-function-return"></a>

`DOUBLE PRECISION` value of the maximum first coordinate\. 

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_XMax-function-examples"></a>

The following SQL returns the largest first coordinate of a linestring\. 

```
SELECT ST_XMax(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_xmax
-----------
 77.42
```