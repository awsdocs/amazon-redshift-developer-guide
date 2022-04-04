# ST\_IsRing<a name="ST_IsRing-function"></a>

ST\_IsRing returns true if the input linestring is a ring\. A linestring is a ring if it is closed and simple\. 

## Syntax<a name="ST_IsRing-function-syntax"></a>

```
ST_IsRing(geom)
```

## Arguments<a name="ST_IsRing-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The geometry must be a `LINESTRING`\.

## Return type<a name="ST_IsRing-function-return"></a>

`BOOLEAN`

If *geom* is not a `LINESTRING`, then an error is returned\. 

## Examples<a name="ST_IsRing-function-examples"></a>

The following SQL checks if the specified linestring is a ring\. 

```
SELECT ST_IsRing(ST_GeomFromText('linestring(0 0, 1 1, 1 2, 0 0)'));
```

```
st_isring
-----------
 true
```