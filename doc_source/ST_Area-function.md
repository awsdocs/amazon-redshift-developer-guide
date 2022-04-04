# ST\_Area<a name="ST_Area-function"></a>

For an input geometry, ST\_Area returns the Cartesian area of the 2D projection\. The area units are the same as the units in which the coordinates of the input geometry are expressed\. For points, linestrings, multipoints, and multilinestrings, the function returns 0\. For geometry collections, it returns the sum of the areas of the geometries in the collection\. 

For an input geography, ST\_Area returns the geodesic area of the 2D projection of an input areal geography computed on the spheroid determined by the SRID\. The unit of length is in square meters\. The function returns zero \(0\) for points, multipoints, and linear geographies\. When the input is a geometry collection, the function returns the sum of the areas of the areal geographies in the collection\.

## Syntax<a name="ST_Area-function-syntax"></a>

```
ST_Area(geo)
```

## Arguments<a name="ST_Area-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

## Return type<a name="ST_Area-function-return"></a>

`DOUBLE PRECISION`

If *geo* is null, then null is returned\.

## Examples<a name="ST_Area-function-examples"></a>

The following SQL returns the Cartesian area of a multipolygon\. 

```
SELECT ST_Area(ST_GeomFromText('MULTIPOLYGON(((0 0,10 0,0 10,0 0)),((10 0,20 0,20 10,10 0)))'));
```

```
 st_area
---------
     100
```

The following SQL returns the area of a polygon in a geography\. 

```
SELECT ST_Area(ST_GeogFromText('polygon((34 35, 28 30, 25 34, 34 35))'));
```

```
     st_area
------------------
 201824655743.383
```

The following SQL returns zero for a linear geography\. 

```
SELECT ST_Area(ST_GeogFromText('multipoint(0 0, 1 1, -21.32 121.2)'));
```

```
 st_area
---------
       0
```