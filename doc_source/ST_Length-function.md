# ST\_Length<a name="ST_Length-function"></a>

ST\_Length returns the Cartesian length of an input linear geometry\. The length units are the same as the units in which the coordinates of the input geometry are expressed\. The function returns zero \(0\) for points, multipoints, and areal geometries\. When the input is a geometry collection, the function returns the sum of the lengths of the geometries in the collection\. 

## Syntax<a name="ST_Length-function-syntax"></a>

```
ST_Length(geom)
```

## Arguments<a name="ST_Length-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_Length-function-return"></a>

`DOUBLE PRECISION`

If *geom* is null, then null is returned\.

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