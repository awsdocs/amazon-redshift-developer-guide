# ST\_Distance<a name="ST_Distance-function"></a>

ST\_Distance returns the Euclidean distance between the two input geometry values\. 

## Syntax<a name="ST_Distance-function-syntax"></a>

```
ST_Distance(geom1, geom2)
```

## Arguments<a name="ST_Distance-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Distance-function-return"></a>

`DOUBLE PRECISION` in the same units as the input geometries\.

If *geom1* or *geom2* is null or empty, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Distance-function-examples"></a>

The following SQL returns the distance between two polygons\. 

```
SELECT ST_Distance(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 -3,-2 -1,0 -3,-1 -3))'));
```

```
  st_distance
-----------
1.4142135623731
```