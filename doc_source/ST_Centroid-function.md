# ST\_Centroid<a name="ST_Centroid-function"></a>

ST\_Centroid returns a point that represents a centroid of a geometry as follows:
+ For `POINT` geometries, it returns the point whose coordinates are the average of the coordinates of the points in the geometry\. 
+ For `LINESTRING` geometries, it returns the point whose coordinates are the weighted average of the midpoints of the segments of the geometry, where the weights are the lengths of the segments of the geometry\.
+ For `POLYGON` geometries, it returns the point whose coordinates are the weighted average of the centroids of a triangulation of the areal geometry where the weights are the areas of the triangles in the triangulation\.
+ For geometry collections, it returns the weighted average of the centroids of the geometries of maximum topological dimension in the geometry collection\.

## Syntax<a name="ST_Centroid-function-syntax"></a>

```
ST_Centroid(geom)
```

## Arguments<a name="ST_Centroid-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Centroid-function-return"></a>

`GEOMETRY` 

If *geom* is null, then null is returned\. 

If *geom* is empty, then null is returned\. 

## Examples<a name="ST_Centroid-function-examples"></a>

The following SQL returns central point of an input linestring\. 

```
SELECT ST_AsEWKT(ST_Centroid(ST_GeomFromText('LINESTRING(110 40, 2 3, -10 80, -7 9, -22 -33)', 4326)))
```

```
                     st_asewkt
----------------------------------------------------
 SRID=4326;POINT(15.6965103455214 27.0206782881905)
```