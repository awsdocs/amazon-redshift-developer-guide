# ST\_NPoints<a name="ST_NPoints-function"></a>

ST\_NPoints returns the number of points in an input geometry\. 

## Syntax<a name="ST_NPoints-function-syntax"></a>

```
ST_NPoints(geom)
```

## Arguments<a name="ST_NPoints-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_NPoints-function-return"></a>

`INTEGER`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_NPoints-function-examples"></a>

The following SQL returns the number of points in a linestring\. 

```
SELECT ST_NPoints(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_npoints
-------------
 4
```