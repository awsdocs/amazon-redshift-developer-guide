# ST\_YMax<a name="ST_YMax-function"></a>

ST\_YMax returns the maximum second coordinate of an input geometry\. 

## Syntax<a name="ST_YMax-function-syntax"></a>

```
ST_YMax(geom)
```

## Arguments<a name="ST_YMax-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_YMax-function-return"></a>

`DOUBLE PRECISION` value of the maximum second coordinate\.

If *geom* is empty, then null is returned\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_YMax-function-examples"></a>

The following SQL returns the largest second coordinate of a linestring\. 

```
SELECT ST_YMax(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_ymax
-----------
 29.31
```