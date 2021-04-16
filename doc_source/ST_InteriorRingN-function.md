# ST\_InteriorRingN<a name="ST_InteriorRingN-function"></a>

ST\_InteriorRingN returns a closed linestring corresponding to the interior ring of an input polygon at the index position\. 

## Syntax<a name="ST_InteriorRingN-function-syntax"></a>

```
ST_InteriorRingN(geom, index)
```

## Arguments<a name="ST_InteriorRingN-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

 *index*   
A value of data type `INTEGER` that represents the position of a ring of a one\-based index\. 

## Return type<a name="ST_InteriorRingN-function-return"></a>

`GEOMETRY` of subtype `LINESTRING`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometry\. 

If *geom* or *index* is null, then null is returned\. 

If *index* is out of range, then null is returned\. 

If *geom* is not a polygon, then null is returned\. 

If *geom* is an empty polygon, then null is returned\. 

## Examples<a name="ST_InteriorRingN-function-examples"></a>

The following SQL returns the second ring of the polygon as a closed linestring\. 

```
SELECT ST_AsEWKT(ST_InteriorRingN(ST_GeomFromText('POLYGON((7 9,8 7,11 6,15 8,16 6,17 7,17 10,18 12,17 14,15 15,11 15,10 13,9 12,7 9),(9 9,10 10,11 11,11 10,10 8,9 9),(12 14,15 14,13 11,12 14))'),2));
```

```
st_asewkt
-----------
 LINESTRING(12 14,15 14,13 11,12 14)
```