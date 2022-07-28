# ST\_NPoints<a name="ST_NPoints-function"></a>

ST\_NPoints returns the number of nonempty points in an input geometry or geography\. 

## Syntax<a name="ST_NPoints-function-syntax"></a>

```
ST_NPoints(geo)
```

## Arguments<a name="ST_NPoints-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

## Return type<a name="ST_NPoints-function-return"></a>

`INTEGER`

If *geo* is an empty point, then `0` is returned\. 

If *geo* is null, then null is returned\. 

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

The following SQL returns the number of points in a linestring in a geography\. 

```
SELECT ST_NPoints(ST_GeogFromText('LINESTRING(110 40, 2 3, -10 80, -7 9)'));
```

```
st_npoints
-------------
 4
```