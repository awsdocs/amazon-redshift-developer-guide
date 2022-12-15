# ST\_GeomFromGeoHash<a name="ST_GeomFromGeoHash-function"></a>

ST\_GeomFromGeoHash constructs a geometry object from the geohash representation of an input geometry\. ST\_GeomFromGeoHash returns a two\-dimensional \(2D\) geometry with the spatial reference identifier \(SRID\) of zero \(0\)\. For more information about the geohash format, see [Geohash](https://en.wikipedia.org/wiki/Geohash) in Wikipedia\. 

## Syntax<a name="ST_GeomFromGeoHash-function-syntax"></a>

```
ST_GeomFromGeoHash(geohash_string)
```

```
ST_GeomFromGeoHash(geohash_string, precision)
```

## Arguments<a name="ST_GeomFromGeoHash-function-arguments"></a>

 *geohash\_string*   
A value of data type `VARCHAR` or an expression that evaluates to a `VARCHAR` type, that is a geohash representation of a geometry\.

 *precision*   
A value of data type `INTEGER` that represents the precision of the geohash\. The value is the number of characters of the geohash to be used as precision\. If the value is not specified, less than zero, or greater than the *geohash\_string* length\. then the *geohash\_string* length is used\.

## Return type<a name="ST_GeomFromGeoHash-function-return"></a>

`GEOMETRY`

If *geohash\_string* is null, then null is returned\. 

If *geohash\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeomFromGeoHash-function-examples"></a>

The following SQL returns a polygon with high precision\. 

```
SELECT ST_AsText(ST_GeomFromGeoHash('9qqj7nmxncgyy4d0dbxqz0'));
```

```
 st_asewkt       
-----------------------
 POLYGON((-115.172816 36.114646,-115.172816 36.114646,-115.172816 36.114646,-115.172816 36.114646,-115.172816 36.114646))
```

The following SQL returns a point with high precision\. 

```
SELECT ST_AsText(ST_GeomFromGeoHash('9qqj7nmxncgyy4d0dbxqz00'));
```

```
 st_asewkt       
-----------------------
 POINT(-115.172816 36.114646)
```

The following SQL returns a polygon with low precision\. 

```
SELECT ST_AsText(ST_GeomFromGeoHash('9qq'));
```

```
 st_asewkt       
-----------------------
 POLYGON((-115.3125 35.15625,-115.3125 36.5625,-113.90625 36.5625,-113.90625 35.15625,-115.3125 35.15625))
```

The following SQL returns a polygon with precision 3\. 

```
SELECT ST_AsText(ST_GeomFromGeoHash('9qqj7nmxncgyy4d0dbxqz0', 3));
```

```
 st_asewkt       
-----------------------
 POLYGON((-115.3125 35.15625,-115.3125 36.5625,-113.90625 36.5625,-113.90625 35.15625,-115.3125 35.15625))
```