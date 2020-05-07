# ST\_Touches<a name="ST_Touches-function"></a>

ST\_Touches returns true if the two input geometries touch\. The two geometries touch if they are nonempty, intersect, and have no interior points in common\. 

## Syntax<a name="ST_Touches-function-syntax"></a>

```
ST_Touches(geom1, geom2)
```

## Arguments<a name="ST_Touches-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Touches-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Touches-function-examples"></a>

The following SQL checks if a polygon touches a linestring\. 

```
SELECT ST_Touches(ST_GeomFromText('POLYGON((0 0,10 0,0 10,0 0))'), ST_GeomFromText('LINESTRING(20 10,20 0,10 0)'));
```

```
 st_touches              
-------------
 t
```