# ST\_IsValid<a name="ST_IsValid-function"></a>

ST\_IsValid returns true if the input geometry is valid\. For more information about the definition of a valid geometry, see [Geometric validity](spatial-terminology.md#spatial-terminology-validity)\. 

## Syntax<a name="ST_IsValid-function-syntax"></a>

```
ST_IsValid(geom)
```

## Arguments<a name="ST_IsValid-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_IsValid-function-return"></a>

`BOOLEAN`

If *geom* is null, then null is returned\. 

## Examples<a name="ST_IsValid-function-examples"></a>

The following SQL checks if the specified polygon is valid\. In this example, the polygon is invalid because the interior of the polygon isn't simply connected\. 

```
SELECT ST_IsValid(ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0),(5 0,10 5,5 10,0 5,5 0))'));
```

```
 st_isvalid
-----------
 false
```