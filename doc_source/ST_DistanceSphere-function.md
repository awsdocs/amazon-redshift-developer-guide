# ST\_DistanceSphere<a name="ST_DistanceSphere-function"></a>

ST\_DistanceSphere returns the distance between two point geometries lying on a sphere\. 

## Syntax<a name="ST_DistanceSphere-function-syntax"></a>

```
ST_DistanceSphere(geom1, geom2)
```

```
ST_DistanceSphere(geom1, geom2, radius)
```

## Arguments<a name="ST_DistanceSphere-function-arguments"></a>

 *geom1*   
A point value in degrees of data type `GEOMETRY` lying on a sphere\. The first coordinate of the point is the longitude value\. The second coordinate of the point is the latitude value\. 

 *geom2*   
A point value in degrees of data type `GEOMETRY` lying on a sphere\. The first coordinate of the point is the longitude value\. The second coordinate of the point is the latitude value\. 

 *radius*   
The radius of a sphere of data type `DOUBLE PRECISION`\. If no *radius* is provided, the sphere defaults to Earth and the radius is computed from the World Geodetic System \(WGS\) 84 representation of the ellipsoid\. 

## Return type<a name="ST_DistanceSphere-function-return"></a>

`DOUBLE PRECISION` in the same units as the radius\. 

If *geom1* or *geom2* is null or empty, then null is returned\. 

If no *radius* is provided, then the result is in meters along the Earth's surface\. 

If *radius* is a negative number, then an error is returned\. 

If *geom1* and *geom2* don't have the same value for the spatial reference system identifier \(SRID\), then an error is returned\. 

If *geom1* or *geom2* is not a point, then an error is returned\. 

## Examples<a name="ST_DistanceSphere-function-examples"></a>

The following example SQL computes the distances in kilometers between three airport locations in Germany: Berlin Tegel \(TXL\), Munich International \(MUC\), and Frankfurt International \(FRA\)\. 

```
WITH airports_raw(code,lon,lat) AS (
(SELECT 'MUC', 11.786111, 48.353889) UNION
(SELECT 'FRA', 8.570556, 50.033333) UNION
(SELECT 'TXL', 13.287778, 52.559722)),
airports1(code,location) AS (SELECT code, ST_Point(lon, lat) FROM airports_raw),
airports2(code,location) AS (SELECT * from airports1)
SELECT (airports1.code || ' <-> ' || airports2.code) AS airports,
round(ST_DistanceSphere(airports1.location, airports2.location) / 1000, 0) AS distance_in_km
FROM airports1, airports2 WHERE airports1.code < airports2.code ORDER BY 1;
```

```
  airports   | distance_in_km 
-------------+----------------
 FRA <-> MUC |            299
 FRA <-> TXL |            432
 MUC <-> TXL |            480
```