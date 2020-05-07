# ST\_AsEWKT<a name="ST_AsEWKT-function"></a>

ST\_AsEWKT returns the extended well\-known text \(EWKT\) representation of an input geometry\. 

## Syntax<a name="ST_AsEWKT-function-syntax"></a>

```
ST_AsEWKT(geom)
```

```
ST_AsEWKT(geom, precision)
```

## Arguments<a name="ST_AsEWKT-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

 *precision*   
A value of data type `INTEGER`\. The coordinates of *geom* are displayed using the specified precision 1–20\. If *precision* is not specified, the default is 15\. 

## Return type<a name="ST_AsEWKT-function-return"></a>

`VARCHAR`

If *geom* is null, then null is returned\. 

If *precision* is null, then null is returned\. 

If the result is larger than a 64\-KB `VARCHAR`, then an error is returned\. 

## Examples<a name="ST_AsEWKT-function-examples"></a>

The following SQL returns the EWKT representation of a linestring\. 

```
SELECT ST_AsEWKT(ST_GeomFromText('LINESTRING(3.141592653589793 -6.283185307179586,2.718281828459045 -1.414213562373095)', 4326));
```

```
st_asewkt
--------------------------------
 SRID=4326;LINESTRING(3.14159265358979 -6.28318530717959,2.71828182845905 -1.41421356237309)
```

The following SQL returns the EWKT representation of a linestring\. The coordinates of the geometries are displayed with six digits of precision\. 

```
SELECT ST_AsEWKT(ST_GeomFromText('LINESTRING(3.141592653589793 -6.283185307179586,2.718281828459045 -1.414213562373095)', 4326), 6);
```

```
st_asewkt
--------------------------------
 SRID=4326;LINESTRING(3.14159 -6.28319,2.71828 -1.41421)
```