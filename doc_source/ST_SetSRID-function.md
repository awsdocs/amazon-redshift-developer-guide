# ST\_SetSRID<a name="ST_SetSRID-function"></a>

ST\_SetSRID returns a geometry that is the same as input geometry, except updated with the value input for the spatial reference system identifier \(SRID\)\. 

## Syntax<a name="ST_SetSRID-function-syntax"></a>

```
ST_SetSRID(geom, srid)
```

## Arguments<a name="ST_SetSRID-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *srid*   
A value of data type `INTEGER` that represents a SRID\. 

## Return type<a name="ST_SetSRID-function-return"></a>

`GEOMETRY`

The SRID value of the returned geometry is set to *srid*\. 

If *geom* or *srid* is null, then null is returned\.

If *srid* is negative, then an error is returned\.

## Examples<a name="ST_SetSRID-function-examples"></a>

The following SQL sets the SRID value of a linestring\.

```
SELECT ST_AsEWKT(ST_SetSRID(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'),50));
```

```
st_asewkt
-------------
 SRID=50;LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)
```