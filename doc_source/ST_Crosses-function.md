# ST\_Crosses<a name="ST_Crosses-function"></a>

ST\_Crosses returns true if the two input geometries cross each other\. 

## Syntax<a name="ST_Crosses-function-syntax"></a>

```
ST_Crosses(geom1, geom2)
```

## Arguments<a name="ST_Crosses-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Crosses-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

## Examples<a name="ST_Crosses-function-examples"></a>

The following SQL checks if the first polygon crosses the second multipoint\. In this example, the multipoint intersects both the interior and exterior of the polygon, which is why ST\_Crosses returns true\.

```
SELECT ST_Crosses (ST_GeomFromText('polygon((0 0,10 0,10 10,0 10,0 0))'), ST_GeomFromText('multipoint(5 5,0 0,-1 -1)'));
```

```
st_crosses              
-------------
 true
```

The following SQL checks if the first polygon crosses the second multipoint\. In this example, the multipoint intersects the exterior of the polygon but not its interior, which is why ST\_Crosses returns false\.

```
SELECT ST_Crosses (ST_GeomFromText('polygon((0 0,10 0,10 10,0 10,0 0))'), ST_GeomFromText('multipoint(0 0,-1 -1)'));
```

```
st_crosses              
-------------
 false
```