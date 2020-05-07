# ST\_MemSize<a name="ST_MemSize-function"></a>

ST\_MemSize returns the amount of memory space \(in bytes\) used by the input geometry\. This size depends on the Amazon Redshift internal representation of the geometry and thus can change if the internal representation changes\. You can use this size as an indication of the relative size of geometry objects in Amazon Redshift\. 

## Syntax<a name="ST_MemSize-function-syntax"></a>

```
ST_MemSize(geom)
```

## Arguments<a name="ST_MemSize-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_MemSize-function-return"></a>

`INTEGER` representing the inherent dimension of *geom*\. 

If *geom* is null, then null is returned\. 

## Examples<a name="ST_MemSize-function-examples"></a>

The following SQL returns the memory size of a geometry collection\. 

```
SELECT ST_MemSize(ST_GeomFromText('GEOMETRYCOLLECTION(POLYGON((0 0,10 0,0 10,0 0)),LINESTRING(20 10,20 0,10 0))'))::varchar + ' bytes';
```

```
 ?column?  
-----------
 172 bytes
```