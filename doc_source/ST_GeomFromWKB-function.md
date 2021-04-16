# ST\_GeomFromWKB<a name="ST_GeomFromWKB-function"></a>

ST\_GeomFromWKB constructs a geometry object from a hexadecimal well\-known binary \(WKB\) representation of an input geometry\. 

## Syntax<a name="ST_GeomFromWKB-function-syntax"></a>

```
ST_GeomFromWKB(wkb_string)
```

```
ST_GeomFromWKB(wkb_string, srid)
```

## Arguments<a name="ST_GeomFromWKB-function-arguments"></a>

 *wkb\_string*   
A value of data type `VARCHAR` that is a hexadecimal WKB representation of a geometry\.

 *srid*   
A value of data type `INTEGER` that is a spatial reference identifier \(SRID\)\. If an SRID value is provided, the returned geometry has this SRID value\. Otherwise, the SRID value of the returned geometry is set to 0\.

## Return type<a name="ST_GeomFromWKB-function-return"></a>

`GEOMETRY`

If *wkb\_string* or *srid* is null, then null is returned\. 

If *srid* is negative, then null is returned\. 

If *wkb\_string* is not valid, then an error is returned\. 

If *srid* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromWKB-function-examples"></a>

The following SQL constructs a polygon from a WKB value and returns the WKT representation of a polygon\. 

```
SELECT ST_AsText(ST_GeomFromWKB('01030000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000'));            
```

```
 st_astext
--------------------------------
 POLYGON((0 0,0 1,1 1,1 0,0 0))
```