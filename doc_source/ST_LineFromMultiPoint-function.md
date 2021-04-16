# ST\_LineFromMultiPoint<a name="ST_LineFromMultiPoint-function"></a>

ST\_LineFromMultiPoint returns a linestring from an input multipoint geometry\. The order of the points is preserved\. The spatial reference system identifier \(SRID\) of the returned geometry is the same as that of the input geometry\. 

## Syntax<a name="ST_LineFromMultiPoint-function-syntax"></a>

```
ST_LineFromMultiPoint(geom)
```

## Arguments<a name="ST_LineFromMultiPoint-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `MULTIPOINT`\. 

## Return type<a name="ST_LineFromMultiPoint-function-return"></a>

`GEOMETRY`

If *geom* is null, then null is returned\. 

If *geom* isn't a `MULTIPOINT`, then error is returned\. 

## Examples<a name="ST_LineFromMultiPoint-function-examples"></a>

The following SQL creates a linestring from a multipoint\. 

```
SELECT ST_AsEWKT(ST_LineFromMultiPoint(ST_GeomFromText('MULTIPOINT(0 0,10 0,10 10,5 5,0 5)',4326)));
```

```
 st_asewkt
---------------------------------------------
 SRID=4326;LINESTRING(0 0,10 0,10 10,5 5,0 5)
```