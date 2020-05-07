# ST\_Polygon<a name="ST_Polygon-function"></a>

ST\_Polygon returns a polygon geometry whose outer ring is the input linestring with the value that was input for the spatial reference system identifier \(SRID\)\. 

## Syntax<a name="ST_Polygon-function-syntax"></a>

```
ST_Polygon(linestring, srid)
```

## Arguments<a name="ST_Polygon-function-arguments"></a>

 *linestring*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING` that represents a linestring\. The *linestring* value must be closed\. 

 *srid*   
A value of data type `INTEGER` that represents a SRID\. 

## Return type<a name="ST_Polygon-function-return"></a>

`GEOMETRY` of subtype `POLYGON`\.

The SRID value of the returned geometry is set to *srid*\. 

If *linestring* or *srid* is null, then null is returned\.

If *linestring* is not a linestring, then an error is returned\.

If *linestring* is not closed, then an error is returned\.

If *srid* is negative, then an error is returned\.

## Examples<a name="ST_Polygon-function-examples"></a>

The following SQL constructs a polygon with an SRID value\. 

```
SELECT ST_AsEWKT(ST_Polygon(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'),4356));
```

```
st_asewkt
-------------
 SRID=4356;POLYGON((77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07))
```