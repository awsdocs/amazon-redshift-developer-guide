# ST\_NumInteriorRings<a name="ST_NumInteriorRings-function"></a>

ST\_NumInteriorRings returns the number of rings in an input polygon geometry\. 

## Syntax<a name="ST_NumInteriorRings-function-syntax"></a>

```
ST_NumInteriorRings(geom)
```

## Arguments<a name="ST_NumInteriorRings-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_NumInteriorRings-function-return"></a>

`INTEGER`

If *geom* is null, then null is returned\. 

If *geom* is not a polygon, then null is returned\. 

## Examples<a name="ST_NumInteriorRings-function-examples"></a>

The following SQL returns the number of interior rings in the input polygon\. 

```
SELECT ST_NumInteriorRings(ST_GeomFromText('POLYGON((0 0,100 0,100 100,0 100,0 0),(1 1,1 5,5 1,1 1),(7 7,7 8,8 7,7 7))'));
```

```
 st_numinteriorrings
-------------
 2
```