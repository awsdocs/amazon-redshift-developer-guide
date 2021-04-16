# ST\_AsBinary<a name="ST_AsBinary-function"></a>

ST\_AsBinary returns the hexadecimal well\-known binary \(WKB\) representation of an input geometry using ASCII hexadecimal characters \(0–9, A–F\)\. 

## Syntax<a name="ST_AsBinary-function-syntax"></a>

```
ST_AsBinary(geom)
```

## Arguments<a name="ST_AsBinary-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="ST_AsBinary-function-return"></a>

`VARCHAR`

If *geom* is null, then null is returned\.

If the result is larger than a 64\-KB `VARCHAR`, then an error is returned\. 

## Examples<a name="ST_AsBinary-function-examples"></a>

The following SQL returns the hexadecimal WKB representation of a polygon\. 

```
SELECT ST_AsBinary(ST_GeomFromText('POLYGON((0 0,0 1,1 1,1 0,0 0))',4326));
```

```
st_asbinary
--------------------------------
01030000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000
```