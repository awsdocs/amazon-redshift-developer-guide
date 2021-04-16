# ST\_Collect<a name="ST_Collect-function"></a>

ST\_Collect has two variants\. One accepts two geometries, and one accepts an aggregate expression\. 

The first variant of ST\_Collect creates a geometry from the input geometries\. This variant works as follows: 
+ If both input geometries are points, then a `MULTIPOINT` with two points is returned\. 
+ If both input geometries are linestrings, then a `MULTILINESTRING` with two linestrings is returned\. 
+ If both input geometries are polygons, then a `MULTIPOLYGON` with two polygons is returned\. 
+ Otherwise, a `GEOMETRYCOLLECTION` with two input geometries is returned\. 

The second variant of ST\_Collect creates a geometry from geometries in a geometry column\. The order of the input geometries is preserved\. This variant works as follows: 
+ If all non\-NULL rows in the input aggregate expression are points, then a multipoint containing all the points in the aggregate expression is returned\. 
+ If all non\-NULL rows in the aggregate expression are linestrings, then a multilinestring containing all the linestrings in the aggregate expression is returned\. 
+ If all non\-NULL rows in the aggregate expression are polygons, the result is a multipolygon containing all the polygons in the aggregate expression is returned\. 
+ Otherwise, a `GEOMETRYCOLLECTION` containing all the geometries in the aggregate expression is returned\. 

The order of the input geometries is preserved\.

## Syntax<a name="ST_Collect-function-syntax"></a>

```
ST_Collect(geom1, geom2)
```

```
ST_Collect(aggregate_expression)
```

## Arguments<a name="ST_Collect-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *aggregate\_expression*   
A column of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Collect-function-return"></a>

`GEOMETRY` of subtype `MULTIPOINT`, `MULTILINESTRING`, `MULTIPOLYGON`, or `GEOMETRYCOLLECTION`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometries\. 

If both *geom1* or *geom2* are null, then null is returned\. 

If all rows of *aggregate\_expression* are null, then null is returned\. 

If *geom1* is null, then a copy of *geom2* is returned\. Likewise, if *geom2* is null, then a copy of *geom1* is returned\.

If *geom1* and *geom2* have different SRID values, then an error is returned\. 

If two geometries in *aggregate\_expression* have different SRID values, then an error is returned\. 

If the returned geometry is larger than the maximum size of a `GEOMETRY`, then an error is returned\. 

## Examples<a name="ST_Collect-function-examples"></a>

The following SQL returns a geometry collection that contains the two input geometries\. 

```
SELECT ST_AsText(ST_Collect(ST_GeomFromText('LINESTRING(0 0,1 1)'), ST_GeomFromText('POLYGON((10 10,20 10,10 20,10 10))')));
```

```
st_astext
-----------
 GEOMETRYCOLLECTION(LINESTRING(0 0,1 1),POLYGON((10 10,20 10,10 20,10 10)))
```

The following SQL collects all the geometries from a table into a geometry collection\. 

```
WITH tbl(g) AS (SELECT ST_GeomFromText('POINT(1 2)', 4326) UNION ALL
SELECT ST_GeomFromText('LINESTRING(0 0,10 0)', 4326) UNION ALL
SELECT ST_GeomFromText('MULTIPOINT(13 4,8 5,4 4)', 4326) UNION ALL
SELECT NULL::geometry UNION ALL
SELECT ST_GeomFromText('POLYGON((0 0,10 0,0 10,0 0))', 4326))
SELECT ST_AsEWKT(ST_Collect(g)) FROM tbl;
```

```
st_astext
-----------
 SRID=4326;GEOMETRYCOLLECTION(POINT(1 2),LINESTRING(0 0,10 0),MULTIPOINT((13 4),(8 5),(4 4)),POLYGON((0 0,10 0,0 10,0 0)))
```

The following SQL collects all geometries in the table grouped by the id column and ordered by this ID\. In this example, resulting geometries are grouped by ID as follows: 
+ id 1 – points in a multipoint\.
+ id 2 – linestrings in a multilinestring\.
+ id 3 – mixed subtypes in a geometry collection\.
+ id 4 – polygons in a multipolygon\.
+ id 5 – null and the result is null\.

```
WITH tbl(id, g) AS (SELECT 1, ST_GeomFromText('POINT(1 2)', 4326) UNION ALL
SELECT 1, ST_GeomFromText('POINT(4 5)', 4326) UNION ALL
SELECT 2, ST_GeomFromText('LINESTRING(0 0,10 0)', 4326) UNION ALL
SELECT 2, ST_GeomFromText('LINESTRING(10 0,20 -5)', 4326) UNION ALL
SELECT 3, ST_GeomFromText('MULTIPOINT(13 4,8 5,4 4)', 4326) UNION ALL
SELECT 3, ST_GeomFromText('MULTILINESTRING((-1 -1,-2 -2),(-3 -3,-5 -5))', 4326) UNION ALL
SELECT 4, ST_GeomFromText('POLYGON((0 0,10 0,0 10,0 0))', 4326) UNION ALL
SELECT 4, ST_GeomFromText('POLYGON((20 20,20 30,30 20,20 20))', 4326) UNION ALL
SELECT 1, NULL::geometry UNION ALL SELECT 2, NULL::geometry UNION ALL
SELECT 5, NULL::geometry UNION ALL SELECT 5, NULL::geometry)
SELECT id, ST_AsEWKT(ST_Collect(g)) FROM tbl GROUP BY id ORDER BY id;
```

```
 id |                                                 st_asewkt                                                 
----+-----------------------------------------------------------------------------------------------------------
  1 | SRID=4326;MULTIPOINT((1 2),(4 5))
  2 | SRID=4326;MULTILINESTRING((0 0,10 0),(10 0,20 -5))
  3 | SRID=4326;GEOMETRYCOLLECTION(MULTIPOINT((13 4),(8 5),(4 4)),MULTILINESTRING((-1 -1,-2 -2),(-3 -3,-5 -5)))
  4 | SRID=4326;MULTIPOLYGON(((0 0,10 0,0 10,0 0)),((20 20,20 30,30 20,20 20)))
  5 |
```