# ST\_GeomFromEWKT<a name="ST_GeomFromEWKT-function"></a>

ST\_GeomFromEWKT constructs a geometry object from the extended well\-known text \(EWKT\) representation of an input geometry\. 

## Syntax<a name="ST_GeomFromEWKT-function-syntax"></a>

```
ST_GeomFromEWKT(ewkt_string)
```

## Arguments<a name="ST_GeomFromEWKT-function-arguments"></a>

 *ewkt\_string*   
A value of data type `VARCHAR` or an expression that evaluates to a `VARCHAR` type, that is an EWKT representation of a geometry\.

## Return type<a name="ST_GeomFromEWKT-function-return"></a>

`GEOMETRY`

If *ewkt\_string* is null, then null is returned\. 

If *ewkt\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromEWKT-function-examples"></a>

The following SQL constructs a multilinestring from an EWKT value and returns a geometry\. It also returns the ST\_AsEWKT result of the geometry\. 

```
SELECT ST_GeomFromEWKT('SRID=4326;MULTILINESTRING((1 0,1 0),(2 0,3 0),(4 0,5 0,6 0))') as geom, ST_AsEWKT(geom);
```

```
                                                                                                                                                       geom                                                                                                                                                       |                          st_asewkt                           
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------
 0105000020E610000003000000010200000002000000000000000000F03F0000000000000000000000000000F03F00000000000000000102000000020000000000000000000040000000000000000000000000000008400000000000000000010200000003000000000000000000104000000000000000000000000000001440000000000000000000000000000018400000000000000000 | SRID=4326;MULTILINESTRING((1 0,1 0),(2 0,3 0),(4 0,5 0,6 0))
```