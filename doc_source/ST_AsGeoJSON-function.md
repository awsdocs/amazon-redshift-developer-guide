# ST\_AsGeoJSON<a name="ST_AsGeoJSON-function"></a>

ST\_AsGeoJSON returns the GeoJSON representation of an input geometry or geography\. For more information about GeoJSON, see [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) in Wikipedia\.

For 3DZ and 4D geometries, the output geometry is a 3DZ projection of the input 3DZ or 4D geometry\. That is, the `x`, `y`, and `z` coordinates are present in the output\. For 3DM geometries, the output geometry is a 2D projection of the input 3DM geometry\. That is, only `x` and `y` coordinates are present in the output\.

For input geographies, ST\_AsGeoJSON returns the GeoJSON representation of an input geography\. The coordinates of the geography are displayed using the specified precision\. 

## Syntax<a name="ST_AsGeoJSON-function-syntax"></a>

```
ST_AsGeoJSON(geo)
```

```
ST_AsGeoJSON(geo, precision)
```

## Arguments<a name="ST_AsGeoJSON-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

 *precision*   
A value of data type `INTEGER`\. For geometries, the coordinates of *geo* are displayed using the specified precision 1–20\. If *precision* is not specified, the default is 15\. For geographies, the coordinates of *geo* are displayed using the specified precision\. If *precision* is not specified, the default is 15\.

## Return type<a name="ST_AsGeoJSON-function-return"></a>

`VARCHAR`

If *geo* is null, then null is returned\. 

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

The following SQL returns the GeoJSON representation of a geography\. 

```
SELECT ST_AsGeoJSON(ST_GeogFromText('LINESTRING(110 40, 2 3, -10 80, -7 9)'));
```

```
                             st_asgeojson
----------------------------------------------------------------------
 {"type":"LineString","coordinates":[[110,40],[2,3],[-10,80],[-7,9]]}
```