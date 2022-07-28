# ST\_Distance<a name="ST_Distance-function"></a>

For input geometries, ST\_Distance returns the minimum Euclidean distance between the 2D projections of the two input geometry values\. 

For 3DM, 3DZ, 4D geometries, ST\_Distance returns the Euclidean distance between the 2D projections of two input geometry values\.

For input geographies, ST\_Distance returns the geodesic distance of the two 2D points\. The unit of distance is in meters\. For geographies other than points and empty points an error is returned\.

## Syntax<a name="ST_Distance-function-syntax"></a>

```
ST_Distance(geo1, geo2)
```

## Arguments<a name="ST_Distance-function-arguments"></a>

 *geo1*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\. The data type of *geo1* must be the same as *geo2*\.

 *geo2*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\. The data type of *geo2* must be the same as *geo1*\.

## Return type<a name="ST_Distance-function-return"></a>

`DOUBLE PRECISION` in the same units as the input geometries or geographies\.

If *geo1* or *geo2* is null or empty, then null is returned\. 

If *geo1* and *geo2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geo1* or *geo2* is a geometry collection, then an error is returned\. 

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

The following SQL returns the distance between two points in a geography\. 

```
SELECT ST_Distance(ST_GeogFromText('point(1 1)'),ST_GeogFromText('point( -21.32 121.2)'));
```

```
   st_distance
------------------
 13090291.8796529
```