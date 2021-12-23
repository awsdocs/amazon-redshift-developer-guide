# ST\_Transform<a name="ST_Transform-function"></a>

ST\_Transform returns a new geometry with coordinates that are transformed in a spatial reference system defined by the input spatial reference system identifier \(SRID\)\. 

## Syntax<a name="ST_Transform-function-syntax"></a>

```
ST_Transform(geom, srid)
```

## Arguments<a name="ST_Transform-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *srid*   
A value of data type `INTEGER` that represents an SRID\. 

## Return type<a name="ST_Transform-function-return"></a>

`GEOMETRY`\.

The SRID value of the returned geometry is set to *srid*\. 

If *geom* or *srid* is null, then null is returned\.

If the SRID value associated with the input *geom* does not exist, then an error is returned\.

If *srid* does not exist, then an error is returned\.

## Examples<a name="ST_Transform-function-examples"></a>

The following SQL transforms the SRID of an empty geometry collection\. 

```
SELECT ST_AsEWKT(ST_Transform(ST_GeomFromText('GEOMETRYCOLLECTION EMPTY', 3857), 4326));
```

```
             st_asewkt
------------------------------------
 SRID=4326;GEOMETRYCOLLECTION EMPTY
```

The following SQL transforms the SRID of a linestring\. 

```
SELECT ST_AsEWKT(ST_Transform(ST_GeomFromText('LINESTRING(110 40, 2 3, -10 80, -7 9, -22 -33)', 4326), 26918));
```

```
                                                                                            st_asewkt
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 SRID=26918;LINESTRING(73106.6977300955 15556182.9688576,14347201.5059964 1545178.32934967,1515090.41262989 9522193.25115316,10491250.83295 2575457.28410878,5672303.72135968 -5233682.61176205)
```

The following SQL transforms the SRID of a polygon\. 

```
SELECT ST_AsEWKT(ST_Transform(ST_GeomFromText('POLYGON Z ((-10 10 -7, -65 10 -6, -10 64 -5, -10 10 -7), (-11 11 5, -11 12 6, -12 11 7, -11 11 5))', 6989), 6317));
```

```
                                                                                                                                                                                                                      st_asewkt
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 SRID=6317;POLYGON Z ((6186430.2771091 -1090834.57212608 1100247.33216237,2654831.67853801 -5693304.90741276 1100247.50581055,2760987.41750022 -486836.575101877 5709710.44137268,6186430.2771091 -1090834.57212608 1100247.33216237),(6146675.25029258 -1194792.63532103 1209007.1115113,6125027.87562215 -1190584.81194058 1317403.77865723,6124888.99555252 -1301885.3455052 1209007.49312929,6146675.25029258 -1194792.63532103 1209007.1115113))
```