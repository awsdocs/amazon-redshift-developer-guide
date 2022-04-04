# AddBBox<a name="AddBBox-function"></a>

AddBBox returns a copy of the input geometry that supports encoding with a precomputed bounding box\. For more information about support for bounding boxes, see [Bounding box](spatial-terminology.md#spatial-terminology-bounding-box)\.

## Syntax<a name="AddBBox-function-syntax"></a>

```
AddBBox(geom)
```

## Arguments<a name="AddBBox-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="AddBBox-function-return"></a>

`GEOMETRY`

If *geom* is null, then null is returned\.

## Examples<a name="AddBBox-function-examples"></a>

The following SQL returns a copy of an input polygon geometry that supports being encoded with a bounding box\. 

```
SELECT ST_AsText(AddBBox(ST_GeomFromText('POLYGON((0 0,1 0,0 1,0 0))')));
```

```
 st_astext
----------
 POLYGON((0 0,1 0,0 1,0 0))
```