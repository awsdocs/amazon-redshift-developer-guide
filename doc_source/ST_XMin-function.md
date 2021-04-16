# ST\_XMin<a name="ST_XMin-function"></a>

ST\_XMin returns the minimum first coordinate of an input geometry\. 

## Syntax<a name="ST_XMin-function-syntax"></a>

```
ST_XMin(geom)
```

## Arguments<a name="ST_XMin-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_XMin-function-return"></a>

`DOUBLE PRECISION` value of the minimum first coordinate\. 

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_XMin-function-examples"></a>

The following SQL returns the smallest first coordinate of a linestring\. 

```
SELECT ST_XMin(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_xmin
-----------
 77.27
```