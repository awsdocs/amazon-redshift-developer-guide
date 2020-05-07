# ST\_MakePolygon<a name="ST_MakePolygon-function"></a>

ST\_MakePolygon returns a polygon geometry whose outer ring is the input linestring\. 

## Syntax<a name="ST_MakePolygon-function-syntax"></a>

```
ST_MakePolygon(linestring)
```

## Arguments<a name="ST_MakePolygon-function-arguments"></a>

 *linestring*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. The *linestring* value must be closed\. 

## Return type<a name="ST_MakePolygon-function-return"></a>

`GEOMETRY` of subtype `POLYGON`\. 

The spatial reference system identifier \(SRID\) of the returned geometry is equal to the SRID of the value input as *linestring*\. 

If *linestring* is null, then null is returned\.

If *linestring* is not a linestring, then an error is returned\.

If *linestring* is not closed, then an error is returned\.

## Examples<a name="ST_MakePolygon-function-examples"></a>

The following SQL returns a polygon from an input linestring\. 

```
SELECT ST_AsText(ST_MakePolygon(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)')));
```

```
st_astext
xxxxxxxxxxx
POLYGON((77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07))
```