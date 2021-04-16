# ST\_Equals<a name="ST_Equals-function"></a>

ST\_Equals returns true if the input geometries are geometrically equal\. Geometries are considered geometrically equal if they have equal point sets and their interiors have a nonempty intersection\. 

## Syntax<a name="ST_Equals-function-syntax"></a>

```
ST_Equals(geom1, geom2)
```

## Arguments<a name="ST_Equals-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. This value is compared with *geom1* to determine if it is equal to *geom1*\. 

## Return type<a name="ST_Equals-function-return"></a>

`BOOLEAN`

If *geom1* or *geom2* is null, then an error is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

## Examples<a name="ST_Equals-function-examples"></a>

The following SQL checks if the two polygons are geometrically equal\. 

```
SELECT ST_Equals(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeomFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'));
```

```
st_equals
-----------
 false
```

The following SQL checks if the two linestrings are geometrically equal\. 

```
SELECT ST_Equals(ST_GeomFromText('LINESTRING(1 0,10 0)'), ST_GeomFromText('LINESTRING(1 0,5 0,10 0)'));
```

```
st_equals
-----------
 true
```