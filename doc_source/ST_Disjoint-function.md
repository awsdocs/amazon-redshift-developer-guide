# ST\_Disjoint<a name="ST_Disjoint-function"></a>

ST\_Disjoint returns true if the two input geometries have no points in common\. 

## Syntax<a name="ST_Disjoint-function-syntax"></a>

```
ST_Disjoint(geom1, geom2)
```

## Arguments<a name="ST_Disjoint-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Disjoint-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Disjoint-function-examples"></a>

The following SQL checks if the first polygon is disjoint from the second polygon\. 

```
SELECT ST_Disjoint(ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0),(2 2,2 5,5 5,5 2,2 2))'), ST_Point(4, 4));
```

```
st_disjoint               
-----------
 true
```