# ST\_IsClosed<a name="ST_IsClosed-function"></a>

ST\_IsClosed returns true if the input geometry is closed\. The following rules define a closed geometry: 
+ The input geometry is a point or a multipoint\. 
+ The input geometry is a linestring, and the start and end points of the linestring coincide\. 
+ The input geometry is a nonempty multilinestring and all its linestrings are closed\. 
+ The input geometry is a nonempty polygon, all polygon's rings are nonempty, and the start and end points of all its rings coincide\. 
+ The input geometry is a nonempty multipolygon and all its polygons are closed\.
+ The input geometry is a nonempty geometry collection and all its components are closed\. 

## Syntax<a name="ST_IsClosed-function-syntax"></a>

```
ST_IsClosed(geom)
```

## Arguments<a name="ST_IsClosed-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_IsClosed-function-return"></a>

`BOOLEAN`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_IsClosed-function-examples"></a>

The following SQL checks if the polygon is closed\. 

```
SELECT ST_IsClosed(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'));
```

```
st_isclosed
-----------
 true
```