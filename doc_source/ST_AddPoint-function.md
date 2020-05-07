# ST\_AddPoint<a name="ST_AddPoint-function"></a>

ST\_AddPoint returns a linestring geometry that is the same as the input geometry with a point added\. If an index is provided, then the point is added at the index position\. If the index is \-1 or not provided, then the point is appended to the linestring\. 

The index is zero\-based\. The spatial reference system identifier \(SRID\) of the result is the same as that of the input geometry\. 

## Syntax<a name="ST_AddPoint-function-syntax"></a>

```
ST_AddPoint(geom1, geom2)
```

```
ST_AddPoint(geom1, geom2, index)
```

## Arguments<a name="ST_AddPoint-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

 *index*   
A value of data type `INTEGER` that represents the position of a zero\-based index\. 

## Return type<a name="ST_AddPoint-function-return"></a>

`GEOMETRY` 

If *geom1*, *geom2*, or *index* is null, then null is returned\. 

If *geom1* is not a `LINESTRING`, then an error is returned\. 

If *geom2* is not a `POINT`, then an error is returned\. 

If *index* is out of range, then an error is returned\. Valid values for the index position are \-1 or a value between 0 and `ST_NumPoints(geom1)`\. 

## Examples<a name="ST_AddPoint-function-examples"></a>

The following SQL adds a point to a linestring to make it a closed linestring\. 

```
WITH tmp(g) AS (SELECT ST_GeomFromText('LINESTRING(0 0,10 0,10 10,5 5,0 5)',4326))
SELECT ST_AsEWKT(ST_AddPoint(g, ST_StartPoint(g))) FROM tmp;
```

```
 st_asewkt
------------------------------------------------
 SRID=4326;LINESTRING(0 0,10 0,10 10,5 5,0 5,0 0)
```

The following SQL adds a point to a specific position in a linestring\. 

```
WITH tmp(g) AS (SELECT ST_GeomFromText('LINESTRING(0 0,10 0,10 10,5 5,0 5)',4326))
SELECT ST_AsEWKT(ST_AddPoint(g, ST_SetSRID(ST_Point(5, 10), 4326), 3)) FROM tmp;
```

```
 st_asewkt
------------------------------------------------
 SRID=4326;LINESTRING(0 0,10 0,10 10,5 10,5 5,0 5)
```