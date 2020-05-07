# ST\_SRID<a name="ST_SRID-function"></a>

ST\_SRID returns the spatial reference system identifier \(SRID\) of an input geometry\. 

## Syntax<a name="ST_SRID-function-syntax"></a>

```
ST_SRID(geom)
```

## Arguments<a name="ST_SRID-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_SRID-function-return"></a>

`INTEGER` representing the SRID value of *geom*\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_SRID-function-examples"></a>

The following SQL returns an SRID value of a linestring\.

```
SELECT ST_SRID(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'));
```

```
st_srid
-------------
 0
```