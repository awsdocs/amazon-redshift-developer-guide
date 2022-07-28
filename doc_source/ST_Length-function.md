# ST\_Length<a name="ST_Length-function"></a>

For a linear geometry, ST\_Length returns the Cartesian length of a 2D projection\. The length units are the same as the units in which the coordinates of the input geometry are expressed\. The function returns zero \(0\) for points, multipoints, and areal geometries\. When the input is a geometry collection, the function returns the sum of the lengths of the geometries in the collection\. 

For a geography, ST\_Length returns the geodesic length of the 2D projection of an input linear geography computed on the spheroid determined by the SRID\. The unit of length is in meters\. The function returns zero \(0\) for points, multipoints, and areal geographies\. When the input is a geometry collection, the function returns the sum of the lengths of the geographies in the collection\.

## Syntax<a name="ST_Length-function-syntax"></a>

```
ST_Length(geo)
```

## Arguments<a name="ST_Length-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

## Return type<a name="ST_Length-function-return"></a>

`DOUBLE PRECISION`

If *geo* is null, then null is returned\.

If the SRID value is not found, then an error is returned\.

## Examples<a name="ST_Length-function-examples"></a>

The following SQL returns the Cartesian length of a multilinestring\. 

```
SELECT ST_Length(ST_GeomFromText('MULTILINESTRING((0 0,10 0,0 10),(10 0,20 0,20 10))'));
```

```
st_length
--------------------------------
  44.142135623731
```

The following SQL returns the length of a linestring in a geography\. 

```
SELECT ST_Length(ST_GeogFromText('SRID=4326;LINESTRING(5 0,6 0,4 0)'));
```

```
 st_length 
------------------
 333958.472379804
```

The following SQL returns the length of a point in a geography\. 

```
SELECT ST_Length(ST_GeogFromText('SRID=4326;POINT(4 5)'));
```

```
 st_length 
-----------
 0
```