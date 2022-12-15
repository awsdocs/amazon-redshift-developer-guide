# ST\_GeomFromGeoJSON<a name="ST_GeomFromGeoJSON-function"></a>

ST\_GeomFromGeoJSON constructs a geometry object from the GeoJSON representation of an input geometry\. For more information about the GeoJSON format, see [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) in Wikipedia\. 

If there is at least one point with three or more coordinates, the resulting geometry is 3DZ, where the Z component is zero for the points that have only two coordinates\. If all points in the input GeoJSON contain two coordinates or are empty, ST\_GeomFromGeoJSON returns a 2D geometry\. The returned geometry always has the spatial reference identifier \(SRID\) of 4326\.

## Syntax<a name="ST_GeomFromGeoJSON-function-syntax"></a>

```
ST_GeomFromGeoJSON(geojson_string)
```

## Arguments<a name="ST_GeomFromGeoJSON-function-arguments"></a>

 *geojson\_string*   
A value of data type `VARCHAR` or an expression that evaluates to a `VARCHAR` type, that is a GeoJSON representation of a geometry\.

## Return type<a name="ST_GeomFromGeoJSON-function-return"></a>

`GEOMETRY`

If *geojson\_string* is null, then null is returned\. 

If *geojson\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromGeoJSON-function-examples"></a>

The following SQL returns a 2D geometry represented in the input GeoJSON\. 

```
SELECT ST_AsEWKT(ST_GeomFromGeoJSON('{"type":"Point","coordinates":[1,2]}'));
```

```
 st_asewkt       
-----------------------
 SRID=4326;POINT(1 2)
```

The following SQL returns a 3DZ geometry represented in the input GeoJSON\. 

```
SELECT ST_AsEWKT(ST_GeomFromGeoJSON('{"type":"LineString","coordinates":[[1,2,3],[4,5,6],[7,8,9]]}'));
```

```
 st_asewkt  
------------------------------------------
 SRID=4326;LINESTRING Z (1 2 3,4 5 6,7 8 9)
```

The following SQL returns 3DZ geometry when only one point has three coordinates while all other points have two coordinates in the input GeoJSON\. 

```
SELECT ST_AsEWKT(ST_GeomFromGeoJSON('{"type":"Polygon","coordinates":[[[0, 0],[0, 1, 8],[1, 0],[0, 0]]]}'));
```

```
 st_asewkt  
------------------------------------------------
 SRID=4326;POLYGON Z ((0 0 0,0 1 8,1 0 0,0 0 0))
```