# ST\_NRings<a name="ST_NRings-function"></a>

ST\_NRings returns the number of rings in an input geometry\. 

## Syntax<a name="ST_NRings-function-syntax"></a>

```
ST_NRings(geom)
```

## Arguments<a name="ST_NRings-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_NRings-function-return"></a>

`INTEGER`

If *geom* is null, then null is returned\. 

The values returned are as follows\.


| Returned value | Geometry subtype | 
| --- | --- | 
| 0 | Returned if *geom* is a `POINT`, `LINESTRING`, `MULTIPOINT`, or `MULTILINESTRING` subtype  | 
| The number of rings\. | Returned if *geom* is a `POLYGON` or `MULTIPOLYGON` subtype | 
| The number of rings in all components | Returned if *geom* is a `GEOMETRYCOLLECTION` subtype | 

## Examples<a name="ST_NRings-function-examples"></a>

The following SQL returns the number of rings in a multipolygon\. 

```
SELECT ST_NRings(ST_GeomFromText('MULTIPOLYGON(((0 0,10 0,0 10,0 0)),((0 0,-10 0,0 -10,0 0)))'));
```

```
 st_nrings
-------------
 2
```