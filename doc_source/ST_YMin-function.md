# ST\_YMin<a name="ST_YMin-function"></a>

ST\_YMin returns the minimum second coordinate of an input geometry\. 

## Syntax<a name="ST_YMin-function-syntax"></a>

```
ST_YMin(geom)
```

## Arguments<a name="ST_YMin-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_YMin-function-return"></a>

`DOUBLE PRECISION` value of the minimum second coordinate\. 

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_YMin-function-examples"></a>

The following SQL returns the smallest second coordinate of a linestring\. 

```
SELECT ST_YMin(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_ymin
-----------
 29.07
```