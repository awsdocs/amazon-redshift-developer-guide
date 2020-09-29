# ST\_Simplify<a name="ST_Simplify-function"></a>

ST\_Simplify returns a simplified copy of the input geometry using the Ramer\-Douglas\-Peucker algorithm with the given tolerance\. The topology of the input geometry might not be preserved\. For more information about the algorithm, see [Ramer–Douglas–Peucker algorithm](https://en.wikipedia.org/wiki/Ramer–Douglas–Peucker_algorithm) in Wikipedia\. 

## Syntax<a name="ST_Simplify-function-syntax"></a>

```
ST_Simplify(geom, tolerance)
```

## Arguments<a name="ST_Simplify-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *tolerance*   
A value of data type `DOUBLE PRECISION` that represents the tolerance level of the Ramer\-Douglas\-Peucker algorithm\. If *tolerance* is a negative number, then zero is used\. 

## Return type<a name="ST_Simplify-function-return"></a>

`GEOMETRY`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_Simplify-function-examples"></a>

The following SQL simplifies the input linestring using a Euclidean distance tolerance of 1 with the Ramer\-Douglas\-Peucker algorithm\. The units of the distance are the same as those of the coordinates of the geometry\.

```
SELECT ST_AsEWKT(ST_Simplify(ST_GeomFromText('LINESTRING(0 0,1 2,1 1,2 2,2 1)'), 1));
```

```
 st_asewkt
-----------
LINESTRING(0 0,1 2,2 1)
```