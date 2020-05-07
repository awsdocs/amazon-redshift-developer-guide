# ST\_Perimeter<a name="ST_Perimeter-function"></a>

ST\_Perimeter returns the Cartesian perimeter \(length of the boundary\) of an input areal geometry\. The perimeter units are the same as the units in which the coordinates of the input geometry are expressed\. The function returns zero \(0\) for points, multipoints, and linear geometries\. When the input is a geometry collection, the function returns the sum of the perimeters of the geometries in the collection\. 

## Syntax<a name="ST_Perimeter-function-syntax"></a>

```
ST_Perimeter(geom)
```

## Arguments<a name="ST_Perimeter-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_Perimeter-function-return"></a>

`DOUBLE PRECISION`

If *geom* is null, then null is returned\.

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