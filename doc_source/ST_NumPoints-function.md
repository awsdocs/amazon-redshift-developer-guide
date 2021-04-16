# ST\_NumPoints<a name="ST_NumPoints-function"></a>

ST\_NumPoints returns the number of points in an input geometry\. 

## Syntax<a name="ST_NumPoints-function-syntax"></a>

```
ST_NumPoints(geom)
```

## Arguments<a name="ST_NumPoints-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_NumPoints-function-return"></a>

`INTEGER`

If *geom* is null, then null is returned\. 

If *geom* is not of subtype `LINESTRING`, then null is returned\. 

## Examples<a name="ST_NumPoints-function-examples"></a>

The following SQL returns the number of points in the input linestring\. 

```
SELECT ST_NumPoints(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_numpoints
-------------
4
```

The following SQL returns null because the input *geom* is not of subtype `LINESTRING`\. 

```
SELECT ST_NumPoints(ST_GeomFromText('MULTIPOINT(1 2,3 4)'));
```

```
st_numpoints
-------------
```