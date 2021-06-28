# ST\_Reverse<a name="ST_Reverse-function"></a>

ST\_Reverse reverses the order of the vertices for linear and areal geometries\. For point or multipoint geometries, a copy of the original geometry is returned\. For geometry collections, ST\_Reverse reverses the order of the vertices for each of the geometries in the collection\.

The dimension of the returned geometry is the same as that of the input geometry\.

## Syntax<a name="ST_Reverse-function-syntax"></a>

```
ST_Reverse(geom)
```

## Arguments<a name="ST_Reverse-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Reverse-function-return"></a>

`GEOMETRY` 

The spatial reference system identifier \(SRID\) of the returned geometry is the same as that of the input geometry\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_Reverse-function-examples"></a>

The following SQL reverses the order of the points in a linestring\. 

```
SELECT ST_AsEWKT(ST_Reverse(ST_GeomFromText('LINESTRING(1 0,2 0,3 0,4 0)', 4326)));
```

```
    st_asewkt
------------------------------------
  SRID=4326;LINESTRING(4 0,3 0,2 0,1 0)
```