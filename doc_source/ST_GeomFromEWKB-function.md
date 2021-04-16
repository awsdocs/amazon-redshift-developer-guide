# ST\_GeomFromEWKB<a name="ST_GeomFromEWKB-function"></a>

ST\_GeomFromEWKB constructs a geometry object from the extended well\-known binary \(EWKB\) representation of an input geometry\. 

## Syntax<a name="ST_GeomFromEWKB-function-syntax"></a>

```
ST_GeomFromEWKB(ewkb_string)
```

## Arguments<a name="ST_GeomFromEWKB-function-arguments"></a>

 *ewkb\_string*   
A value of data type `VARCHAR` that is a hexadecimal EWKB representation of a geometry\.

## Return type<a name="ST_GeomFromEWKB-function-return"></a>

`GEOMETRY`

If *ewkb\_string* is null, then null is returned\. 

If *ewkb\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromEWKB-function-examples"></a>

The following SQL constructs a polygon from an EWKB value and returns the EWKT representation of a polygon\. 

```
SELECT ST_AsEWKT(ST_GeomFromEWKB('0103000020E61000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000'));
```

```
 st_asewkt
--------------------------------
 SRID=4326;POLYGON((0 0,0 1,1 1,1 0,0 0))
```