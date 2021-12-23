# ST\_Perimeter<a name="ST_Perimeter-function"></a>

For an input areal geometry, ST\_Perimeter returns the Cartesian perimeter \(length of the boundary\) of the 2D projection\. The perimeter units are the same as the units in which the coordinates of the input geometry are expressed\. The function returns zero \(0\) for points, multipoints, and linear geometries\. When the input is a geometry collection, the function returns the sum of the perimeters of the geometries in the collection\. 

For an input geography, ST\_Perimeter returns the geodesic perimeter \(length of the boundary\) of the 2D projection of an input areal geography computed on the spheroid determined by the SRID\. The unit of perimeter is in meters\. The function returns zero \(0\) for points, multipoints, and linear geographies\. When the input is a geometry collection, the function returns the sum of the perimeters of the geographies in the collection\.

## Syntax<a name="ST_Perimeter-function-syntax"></a>

```
ST_Perimeter(geo)
```

## Arguments<a name="ST_Perimeter-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

## Return type<a name="ST_Perimeter-function-return"></a>

`DOUBLE PRECISION`

If *geo* is null, then null is returned\.

If the SRID value is not found, then an error is returned\.

## Examples<a name="ST_Perimeter-function-examples"></a>

The following SQL returns the Cartesian perimeter of a multipolygon\. 

```
SELECT ST_Perimeter(ST_GeomFromText('MULTIPOLYGON(((0 0,10 0,0 10,0 0)),((10 0,20 0,20 10,10 0)))'));
```

```
 st_perimeter
--------------------------------
    68.2842712474619
```

The following SQL returns the Cartesian perimeter of a multipolygon\. 

```
SELECT ST_Perimeter(ST_GeomFromText('MULTIPOLYGON(((0 0,10 0,0 10,0 0)),((10 0,20 0,20 10,10 0)))'));
```

```
 st_perimeter
--------------------------------
    68.2842712474619
```

The following SQL returns the perimeter of a polygon in a geography\. 

```
SELECT ST_Perimeter(ST_GeogFromText('SRID=4326;POLYGON((0 0,1 0,0 1,0 0))'));
```

```
 st_perimeter 
------------------
 378790.428393693
```

The following SQL returns the perimeter of a linestring in a geography\. 

```
SELECT ST_Perimeter(ST_GeogFromText('SRID=4326;LINESTRING(5 0,10 0)'));
```

```
 st_perimeter 
--------------
 0
```