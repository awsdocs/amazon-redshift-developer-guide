# ST\_MakePolygon<a name="ST_MakePolygon-function"></a>

ST\_MakePolygon has two variants that return a polygon\. One takes a single geometry, and another takes two geometries\. 
+ The input of the first variant is a linestring that defines the outer ring of the output polygon\.
+ The input of the second variant is a linestring and a multilinestring\. Both of these are empty or closed\. 

  The boundary of the exterior ring of the output polygon is the input linestring, and the boundaries of the interior rings of the polygon are the linestrings in the input multilinestring\. If the input linestring is empty, an empty polygon is returned\. Empty linestrings in the multilinestring are disregarded\. The spatial reference system identifier \(SRID\) of the resulting geometry is the common SRID of the two input geometries\.

## Syntax<a name="ST_MakePolygon-function-syntax"></a>

```
ST_MakePolygon(geom1)
```

```
ST_MakePolygon(geom1, geom2)
```

## Arguments<a name="ST_MakePolygon-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `LINESTRING`\. The *linestring* value must be closed or empty\.

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `MULTILINESTRING`\. 

## Return type<a name="ST_MakePolygon-function-return"></a>

`GEOMETRY` of subtype `POLYGON`\. 

The spatial reference system identifier \(SRID\) of the returned geometry is equal to the SRID of the inputs\. 

If *geom1*, or *geom2* is null, then null is returned\. 

If *geom1* is not a linestring, then an error is returned\. 

If *geom2* is not a multilinestring, then an error is returned\. 

If *geom1* is not closed, then an error is returned\. 

If *geom1* is a single point or is not closed, then an error is returned\. 

If *geom2* contains at least one linestring that has a single point or is not closed, then an error is returned\. 

If *geom1* and *geom2* have different SRID values, then an error is returned\. 

## Examples<a name="ST_MakePolygon-function-examples"></a>

The following SQL returns a polygon from an input linestring\. 

```
SELECT ST_AsText(ST_MakePolygon(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)')));
```

```
 st_astext
---------------
POLYGON((77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07))
```

The following SQL creates a polygon from a closed linestring and a closed multilinestring\. The linestring is used for the exterior ring of the polygon\. The linestrings in the multilinestrings are used for the interior rings of the polygon\. 

```
SELECT ST_AsEWKT(ST_MakePolygon(ST_GeomFromText('LINESTRING(0 0,10 0,10 10,0 10,0 0)'), ST_GeomFromText('MULTILINESTRING((1 1,1 2,2 1,1 1),(3 3,3 4,4 3,3 3))')));
```

```
 st_astext
----------------------------------
POLYGON((0 0,10 0,10 10,0 10,0 0),(1 1,1 2,2 1,1 1),(3 3,3 4,4 3,3 3))
```