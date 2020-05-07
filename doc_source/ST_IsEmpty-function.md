# ST\_IsEmpty<a name="ST_IsEmpty-function"></a>

ST\_IsEmpty returns true if the input geometry is empty\. A geometry is empty if it contains no points\. 

## Syntax<a name="ST_IsEmpty-function-syntax"></a>

```
ST_IsEmpty(geom)
```

## Arguments<a name="ST_IsEmpty-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_IsEmpty-function-return"></a>

`BOOLEAN`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_IsEmpty-function-examples"></a>

The following SQL checks if the specified polygon is empty\. 

```
SELECT ST_IsEmpty(ST_GeomFromText('POLYGON((0 2,1 1,0 -1,0 2))'));
```

```
st_isempty
-----------
 false
```