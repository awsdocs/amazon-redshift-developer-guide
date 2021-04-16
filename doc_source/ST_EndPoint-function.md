# ST\_EndPoint<a name="ST_EndPoint-function"></a>

ST\_EndPoint returns the last point of an input linestring\. The spatial reference system identifier \(SRID\) value of the result is the same as that of the input geometry\. 

## Syntax<a name="ST_EndPoint-function-syntax"></a>

```
ST_EndPoint(geom)
```

## Arguments<a name="ST_EndPoint-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. 

## Return type<a name="ST_EndPoint-function-return"></a>

`GEOMETRY` 

If *geom* is null, then null is returned\. 

If *geom* is empty, then null is returned\. 

If *geom* isn't a `LINESTRING`, then null is returned\. 

## Examples<a name="ST_EndPoint-function-examples"></a>

The following SQL returns an extended well\-known text \(EWKT\) representation of a four\-point `LINESTRING` to a `GEOMETRY` object and returns the end point of the linestring\. 

```
SELECT ST_AsEWKT(ST_StartPoint(ST_GeomFromText('LINESTRING(0 0,10 0,10 10,5 5,0 5)',4326)));
```

```
st_asewkt
-------------
 SRID=4326;POINT(0 5)
```