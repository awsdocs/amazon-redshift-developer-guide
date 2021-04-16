# ST\_SetPoint<a name="ST_SetPoint-function"></a>

ST\_SetPoint returns a linestring with updated coordinates with respect to the input linestring's position as specified by the index\. The new coordinates are the coordinates of the input point\. 

## Syntax<a name="ST_SetPoint-function-syntax"></a>

```
ST_SetPoint(geom1, index, geom2)
```

## Arguments<a name="ST_SetPoint-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. 

 *index*   
A value of data type `INTEGER` that represents the position of a one\-based index\. The value can take negative values\. A `-1` refers to the first point of the linestring from the right, `-2` refers to the second point of the linestring from the right, and so on\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`\. 

## Return type<a name="ST_SetPoint-function-return"></a>

`BOOLEAN`

If *geom1* is not a linestring, then an error is returned\. 

If *index* is not within a valid index range, then an error is returned\. 

If *geom2* is not a point, then an error is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

## Examples<a name="ST_SetPoint-function-examples"></a>

The following SQL returns a new linestring where we set the second point of the input linestring with the specified point\. 

```
SELECT ST_AsText(ST_SetPoint(ST_GeomFromText('LINESTRING(1 2, 3 2, 5 2, 1 2)'), 2, ST_GeomFromText('POINT(7 9)')));
```

```
st_astext              
-------------
 LINESTRING(1 2,3 2,7 9,1 2)
```

The following SQL example returns a new linestring where we set the third point from the right \(the index is negative\) of the linestring with the specified point\. 

```
SELECT ST_AsText(ST_SetPoint(ST_GeomFromText('LINESTRING(1 2, 3 2, 5 2, 1 2)'), -3, ST_GeomFromText('POINT(7 9)')));
```

```
st_astext              
-------------
 LINESTRING(1 2,7 9,5 2,1 2)
```