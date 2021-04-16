# ST\_Boundary<a name="ST_Boundary-function"></a>

ST\_Boundary returns the boundary of an input geometry as follows: 
+ If the input geometry is empty \(that is, it contains no points\) it is returned as is\. 
+ If the input geometry is a point or nonempty multipoint, an empty geometry collection is returned\.
+ If the input is a linestring or a multilinestring, then a multipoint containing all the points on the boundary is returned\. The multipoint might be empty\)\.
+ If the input is a polygon that does not have any interior rings, then a closed linestring representing its boundary is returned\.
+ If the input is a polygon that has interior rings, or is a multipolygon, then a multilinestring is returned\. The multilinestring contains all the boundaries of all the rings in the areal geometry as closed linestrings\.

## Syntax<a name="ST_Boundary-function-syntax"></a>

```
ST_Boundary(geom)
```

## Arguments<a name="ST_Boundary-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_Boundary-function-return"></a>

`GEOMETRY`

If *geom* is null, then null is returned\.

If *geom* is a `GEOMETRYCOLLECTION`, then an error is returned\.

## Examples<a name="ST_Boundary-function-examples"></a>

The following SQL returns the boundary of the input polygon as a multilinestring\. 

```
SELECT ST_AsEWKT(ST_Boundary(ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0),(1 1,1 2,2 1,1 1))')));
```

```
st_asewkt
--------------------
 MULTILINESTRING((0 0,10 0,10 10,0 10,0 0),(1 1,1 2,2 1,1 1))
```