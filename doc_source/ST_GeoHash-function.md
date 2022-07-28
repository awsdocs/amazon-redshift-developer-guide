# ST\_GeoHash<a name="ST_GeoHash-function"></a>

ST\_GeoHash returns the `geohash` representation of the input point with the specified precision\. The default precision value is 20\. For more information about the definition of geohash, see [Geohash](https://en.wikipedia.org/wiki/Geohash) in Wikipedia\.

## Syntax<a name="ST_GeoHash-function-syntax"></a>

```
ST_GeoHash(geom)
```

```
ST_GeoHash(geom, precision)
```

## Arguments<a name="ST_GeoHash-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *precision*   
A value of data type `INTEGER`\. The default is 20\.

## Return type<a name="ST_GeoHash-function-return"></a>

`GEOMETRY`

The function returns the `geohash` representation of the input point\. 

If the input point is empty, the function returns null\. 

If the input geometry is not a point, the function returns an error\. 

## Examples<a name="ST_GeoHash-function-examples"></a>

The following SQL returns the geohash representation of the input point\. 

```
SELECT ST_GeoHash(ST_GeomFromText('POINT(45 -45)'), 25) AS geohash;
```

```
          geohash
---------------------------
 m000000000000000000000gzz
```

The following SQL returns null because the input point is empty\. 

```
SELECT ST_GeoHash(ST_GeomFromText('POINT EMPTY'), 10) IS NULL AS result;
```

```
 result
---------
 true
```