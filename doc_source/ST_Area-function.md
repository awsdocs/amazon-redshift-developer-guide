# ST\_Area<a name="ST_Area-function"></a>

ST\_Area returns the Cartesian area of an input geometry\. The area units are the same as the units in which the coordinates of the input geometry are expressed\. For points, linestrings, multipoints, and multilinestrings, the function returns 0\. For geometry collections, it returns the sum of the areas of the geometries in the collection\. 

## Syntax<a name="ST_Area-function-syntax"></a>

```
ST_Area(geom)
```

## Arguments<a name="ST_Area-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_Area-function-return"></a>

`DOUBLE PRECISION`

If *geom* is null, then null is returned\.

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