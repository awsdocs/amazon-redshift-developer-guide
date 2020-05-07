# ST\_DWithin<a name="ST_DWithin-function"></a>

ST\_DWithin returns true if the Euclidean distance between two input geometry values is not larger than a threshold value\. 

## Syntax<a name="ST_DWithin-function-syntax"></a>

```
ST_DWithin(geom1, geom2, threshold)
```

## Arguments<a name="ST_DWithin-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *threshold*   
A value of data type `DOUBLE PRECISION`\. This value is in the units of the input arguments\. 

## Return type<a name="ST_DWithin-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *threshold* is negative, then an error is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_DWithin-function-examples"></a>

The following SQL checks if the distance between two polygons is within five units\. 

```
SELECT ST_DWithin(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'),5);
```

```
st_dwithin
-----------
 true
```