# ST\_AsEWKB<a name="ST_AsEWKB-function"></a>

ST\_AsEWKB returns the extended well\-known binary \(EWKB\) representation of an input geometry using ASCII hexadecimal characters \(0–9, A–F\)\. 

## Syntax<a name="ST_AsEWKB-function-syntax"></a>

```
ST_AsEWKB(geom)
```

## Arguments<a name="ST_AsEWKB-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_AsEWKB-function-return"></a>

`VARCHAR`

If *geom* is null, then null is returned\. 

If the result is larger than a 64\-KB `VARCHAR`, then an error is returned\. 

## Examples<a name="ST_AsEWKB-function-examples"></a>

The following SQL returns the hexadecimal EWKB representation of a polygon\. 

```
SELECT ST_AsEWKB(ST_GeomFromText('POLYGON((0 0,0 1,1 1,1 0,0 0))',4326));
```

```
st_asewkb
--------------------------------
0103000020E61000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000
```