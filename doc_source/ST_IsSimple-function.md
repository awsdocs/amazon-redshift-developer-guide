# ST\_IsSimple<a name="ST_IsSimple-function"></a>

ST\_IsSimple returns true if the input geometry is simple\. For more information about the definition of a simple geometry, see [Geometric simplicity](spatial-terminology.md#spatial-terminology-simplicity)\. 

## Syntax<a name="ST_IsSimple-function-syntax"></a>

```
ST_IsSimple(geom)
```

## Arguments<a name="ST_IsSimple-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_IsSimple-function-return"></a>

`BOOLEAN`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_IsSimple-function-examples"></a>

The following SQL checks if the specified linestring is simple\. In this example, it isn't simple because it has self\-intersection\. 

```
SELECT ST_IsSimple(ST_GeomFromText('LINESTRING(0 0,10 0,5 5,5 -5)'));
```

```
 st_issimple
-----------
 false
```