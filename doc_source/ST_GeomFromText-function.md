# ST\_GeomFromText<a name="ST_GeomFromText-function"></a>

ST\_GeomFromText constructs a geometry object from a well\-known text \(WKT\) representation of an input geometry\. 

## Syntax<a name="ST_GeomFromText-function-syntax"></a>

```
ST_GeomFromText(wkt_string)
```

```
ST_GeomFromText(wkt_string, srid)
```

## Arguments<a name="ST_GeomFromText-function-arguments"></a>

 *wkt\_string*   
A value of data type `VARCHAR` that is a WKT representation of a geometry\.

 *srid*   
A value of data type `INTEGER` that is a spatial reference identifier \(SRID\)\. If an SRID value is provided, the returned geometry has this SRID value\. Otherwise, the SRID value of the returned geometry is set to zero \(0\)\.

## Return type<a name="ST_GeomFromText-function-return"></a>

`GEOMETRY`

If *wkt\_string* or *srid* is null, then null is returned\. 

If *srid* is negative, then null is returned\. 

If *wkt\_string* is not valid, then an error is returned\. 

If *srid* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromText-function-examples"></a>

The following SQL constructs a geometry object from the WKT representation and SRID value\. 

```
SELECT ST_GeomFromText('POLYGON((0 0,0 1,1 1,1 0,0 0))',4326);
```

```
st_geomfromtext
--------------------------------
0103000020E61000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000
```