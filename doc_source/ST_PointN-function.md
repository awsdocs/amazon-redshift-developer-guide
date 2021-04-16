# ST\_PointN<a name="ST_PointN-function"></a>

ST\_PointN returns a point in a linestring as specified by an index value\. Negative index values are counted backward from the end of the linestring, so that \-1 is the last point\. 

## Syntax<a name="ST_PointN-function-syntax"></a>

```
ST_PointN(geom, index)
```

## Arguments<a name="ST_PointN-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. 

 *index*   
A value of data type `INTEGER` that represents the index of a point in a linestring\. 

## Return type<a name="ST_PointN-function-return"></a>

`GEOMETRY` of subtype `POINT`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is set to 0\. 

If *geom* or *index* is null, then null is returned\. 

If *index* is out of range, then null is returned\. 

If *geom* is empty, then null is returned\. 

If *geom* is not a `LINESTRING`, then null is returned\. 

## Examples<a name="ST_PointN-function-examples"></a>

The following SQL returns an extended well\-known text \(EWKT\) representation of a six\-point `LINESTRING` to a `GEOMETRY` object and returns the point at index 5 of the linestring\. 

```
SELECT ST_AsEWKT(ST_PointN(ST_GeomFromText('LINESTRING(0 0,10 0,10 10,5 5,0 5,0 0)',4326), 5));
```

```
st_asewkt
-------------
 SRID=4326;POINT(0 5)
```