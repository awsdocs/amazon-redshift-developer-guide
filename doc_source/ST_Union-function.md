# ST\_Union<a name="ST_Union-function"></a>

ST\_Union returns a geometry representing the union of two geometries\. That is, it merges the input geometries to produce a resulting geometry with no overlaps\. 

## Syntax<a name="ST_Union-function-syntax"></a>

```
ST_Union(geom1, geom2)
```

## Arguments<a name="ST_Union-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Union-function-return"></a>

`GEOMETRY`

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometries\.

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* or *geom2* are empty, then an empty geometry is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, linestring, or multilinestring, then an error is returned\. 

If *geom1* or *geom2* is not a two\-dimensional \(2D\) geometry, then an error is returned\. 

## Examples<a name="ST_Union-function-examples"></a>

The following SQL returns the non\-empty geometry representing the union of two input geometries\. 

```
SELECT ST_AsEWKT(ST_Union(ST_GeomFromText('POLYGON((0 0,100 100,0 200,0 0))'), ST_GeomFromText('POLYGON((0 0,10 0,0 10,0 0))')));
```

```
        st_asewkt        
-------------------------
 POLYGON((0 0,0 200,100 100,5 5,10 0,0 0))
```