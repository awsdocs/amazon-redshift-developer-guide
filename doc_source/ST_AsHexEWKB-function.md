# ST\_AsHexEWKB<a name="ST_AsHexEWKB-function"></a>

ST\_AsHexEWKB returns the extended well\-known binary \(EWKB\) representation of an input geometry or geography using ASCII hexadecimal characters \(0–9, A–F\)\. For 3DZ, 3DM, and 4D geometries or geographies, ST\_AsHexEWKB uses the PostGIS extended WKB value for the geometry or geography type\.

## Syntax<a name="ST_AsHexEWKB-function-syntax"></a>

```
ST_AsHexEWKB(geo)
```

## Arguments<a name="ST_AsHexEWKB-function-arguments"></a>

 *geo*   
A value of data type `GEOMETRY` or `GEOGRAPHY`, or an expression that evaluates to a `GEOMETRY` or `GEOGRAPHY` type\.

## Return type<a name="ST_AsHexEWKB-function-return"></a>

`VARCHAR`

If *geo* is null, then null is returned\. 

If the result is larger than a 64\-KB `VARCHAR`, then an error is returned\. 

## Examples<a name="ST_AsHexEWKB-function-examples"></a>

The following SQL returns the hexadecimal EWKB representation of a polygon in a geometry\. 

```
SELECT ST_AsHexEWKB(ST_GeomFromText('POLYGON((0 0,0 1,1 1,1 0,0 0))',4326));
```

```
st_ashexewkb
--------------------------------
0103000020E61000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000
```

The following SQL returns the hexadecimal EWKB representation of a polygon in a geography\. 

```
SELECT ST_AsHexEWKB(ST_GeogFromText('POLYGON((0 0,0 1,1 1,1 0,0 0))'));
```

```
st_ashexewkb
--------------------------------
0103000020E61000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000
```