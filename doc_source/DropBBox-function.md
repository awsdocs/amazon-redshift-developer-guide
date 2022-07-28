# DropBBox<a name="DropBBox-function"></a>

DropBBox returns a copy of the input geometry that doesn't support encoding with a precomputed bounding box\. For more information about support for bounding boxes, see [Bounding box](spatial-terminology.md#spatial-terminology-bounding-box)\.

## Syntax<a name="DropBBox-function-syntax"></a>

```
DropBBox(geom)
```

## Arguments<a name="DropBBox-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\.

## Return type<a name="DropBBox-function-return"></a>

`GEOMETRY`

If *geom* is null, then null is returned\.

## Examples<a name="DropBBox-function-examples"></a>

The following SQL returns a copy of an input polygon geometry that doesn't support being encoded with a bounding box\. 

```
SELECT ST_AsText(DropBBox(ST_GeomFromText('POLYGON((0 0,1 0,0 1,0 0))')));
```

```
 st_astext
----------
 POLYGON((0 0,1 0,0 1,0 0))
```