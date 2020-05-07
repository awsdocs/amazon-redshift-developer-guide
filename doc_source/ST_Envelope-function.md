# ST\_Envelope<a name="ST_Envelope-function"></a>

ST\_Envelope returns the minimum bounding box of the input geometry, as follows:
+ If the input geometry is empty, the returned geometry is a copy of the input geometry\. 
+ If the minimum bounding box of the input geometry degenerates to a point, the returned geometry is a point\. 
+ If the minimum bounding box of the input geometry is one\-dimensional, a two\-point linestring is returned\.
+ If none of the preceding is true, the function returns a clockwise\-oriented polygon whose vertices are the corners of the minimum bounding box\.

The spatial reference system identifier \(SRID\) of the returned geometry is the same as that of the input geometry\. 

## Syntax<a name="ST_Envelope-function-syntax"></a>

```
ST_Envelope(geom)
```

## Arguments<a name="ST_Envelope-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_Envelope-function-return"></a>

`GEOMETRY` 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_Envelope-function-examples"></a>

The following SQL converts a well\-known text \(WKT\) representation of a four\-point `LINESTRING` to a `GEOMETRY` object and returns a polygon whose vertices whose corners are the minimum bounding box\. 

```
SELECT ST_AsText(ST_Envelope(ST_GeomFromText('GEOMETRYCOLLECTION(POLYGON((0 0,10 0,0 10,0 0)),LINESTRING(20 10,20 0,10 0))')));
```

```
    st_astext
------------------------------------
  POLYGON((0 0,0 10,20 10,20 0,0 0))
```