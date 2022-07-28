# ST\_Intersection<a name="ST_Intersection-function"></a>

ST\_Intersection returns a geometry representing the point\-set intersection of two geometries\. That is, it returns the portion of the two input geometries that is shared between them\. 

## Syntax<a name="ST_Intersection-function-syntax"></a>

```
ST_Intersection(geom1, geom2)
```

## Arguments<a name="ST_Intersection-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Intersection-function-return"></a>

`GEOMETRY`

If *geom1* and *geom2* don't share any space \(they are disjoint\), then an empty geometry is returned\. 

If *geom1* or *geom2* are empty, then an empty geometry is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is a geometry collection, then an error is returned\. 

If *geom1* or *geom2* is not a two\-dimensional \(2D\) geometry, then an error is returned\. 

## Examples<a name="ST_Intersection-function-examples"></a>

The following SQL returns the non\-empty geometry representing the intersection of two input geometries\. 

```
SELECT ST_AsEWKT(ST_Intersection(ST_GeomFromText('polygon((0 0,100 100,0 200,0 0))'), ST_GeomFromText('polygon((0 0,10 0,0 10,0 0))')));
```

```
        st_asewkt        
-------------------------
 POLYGON((0 0,0 10,5 5,0 0))
```

The following SQL returns an empty geometry when passed disjoint \(non\-intersecting\) input geometries\. 

```
SELECT ST_AsEWKT(ST_Intersection(ST_GeomFromText('linestring(0 100,0 0)'), ST_GeomFromText('polygon((1 0,10 0,1 10,1 0))')));
```

```
    st_asewkt     
------------------
 LINESTRING EMPTY
```