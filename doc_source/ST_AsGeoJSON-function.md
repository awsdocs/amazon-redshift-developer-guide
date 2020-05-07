# ST\_AsGeoJSON<a name="ST_AsGeoJSON-function"></a>

ST\_AsGeoJSON returns the GeoJSON representation of an input geometry\. For more information about GeoJSON, see [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) in Wikipedia\.

## Syntax<a name="ST_AsGeoJSON-function-syntax"></a>

```
ST_AsGeoJSON(geom)
```

```
ST_AsGeoJSON(geom, precision)
```

## Arguments<a name="ST_AsGeoJSON-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

 *precision*   
A value of data type `INTEGER`\. The coordinates of *geom* are displayed using the specified precision 1–20\. If *precision* is not specified, the default is 15\. 

## Return type<a name="ST_AsGeoJSON-function-return"></a>

`VARCHAR`

If *geom* is null, then null is returned\. 

If *precision* is null, then null is returned\. 

If the result is larger than a 64\-KB `VARCHAR`, then an error is returned\. 

## Examples<a name="ST_AsGeoJSON-function-examples"></a>

The following SQL returns the GeoJSON representation of a linestring\. 

```
SELECT ST_AsGeoJSON(ST_GeomFromText('LINESTRING(3.141592653589793 -6.283185307179586,2.718281828459045 -1.414213562373095)'));
```

```
st_asgeojson
----------------------------------------------------------------------------------------------------------------
 {"type":"LineString","coordinates":[[3.14159265358979,-6.28318530717959],[2.71828182845905,-1.41421356237309]]}
```

The following SQL returns the GeoJSON representation of a linestring\. The coordinates of the geometries are displayed with six digits of precision\. 

```
SELECT ST_AsGeoJSON(ST_GeomFromText('LINESTRING(3.141592653589793 -6.283185307179586,2.718281828459045 -1.414213562373095)'), 6);
```

```
st_asgeojson
-----------------------------------------------------------------------------
  {"type":"LineString","coordinates":[[3.14159,-6.28319],[2.71828,-1.41421]]}
```