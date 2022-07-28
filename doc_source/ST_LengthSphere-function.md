# ST\_LengthSphere<a name="ST_LengthSphere-function"></a>

ST\_LengthSphere returns the length of a linear geometry in meters\. For point, multipoint, and areal geometries, ST\_LengthSphere returns 0\. For geometry collections, ST\_LengthSphere returns the total length of the linear geometries in the collection in meters\. 

ST\_LengthSphere interprets the coordinates of each point of the input geometry as longitude and latitude in degrees\. For 3DZ, 3DM, or 4D geometries, only the first two coordinates are used\.

## Syntax<a name="ST_LengthSphere-function-syntax"></a>

```
ST_LengthSphere(geom)
```

## Arguments<a name="ST_LengthSphere-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_LengthSphere-function-return"></a>

`DOUBLE PRECISION` length in meters\. The length computation is based on the spherical model of the Earth whose radius is Earth's mean radius of the World Geodetic System \(WGS\) 84 ellipsoidal model of the Earth\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_LengthSphere-function-examples"></a>

The following example SQL computes the length of a linestring in meters\. 

```
SELECT ST_LengthSphere(ST_GeomFromText('LINESTRING(10 10,45 45)'));
```

```
 st_lengthsphere  
------------------
 5127736.08292556
```