# ST\_NDims<a name="ST_NDims-function"></a>

ST\_NDims returns the coordinate dimension of a geometry\. ST\_NDims doesn't consider the topological dimension of a geometry\. Instead, it returns a constant value depending on the dimension of the geometry\. 

## Syntax<a name="ST_NDims-function-syntax"></a>

```
ST_NDims(geom)
```

## Arguments<a name="ST_NDims-function-arguments"></a>

 *geom*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. 

## Return type<a name="ST_NDims-function-return"></a>

`INTEGER` representing the inherent dimension of *geom*\. 

If *geom* is null, then null is returned\. 

The values returned are as follows\.


| Returned value | Dimension of input geometry | 
| --- | --- | 
| 2 | 2D | 
| 3 | 3DZ or 3DM | 
| 4 | 4D | 

## Examples<a name="ST_NDims-function-examples"></a>

The following SQL returns the number of dimensions of a 2D linestring\. 

```
SELECT ST_NDims(ST_GeomFromText('LINESTRING(0 0,1 1,2 2,0 0)'));
```

```
st_ndims
-------------
 2
```

The following SQL returns the number of dimensions of a 3DZ linestring\. 

```
SELECT ST_NDims(ST_GeomFromText('LINESTRING Z(0 0 3,1 1 3,2 2 3,0 0 3)'));
```

```
st_ndims
-------------
 3
```

The following SQL returns the number of dimensions of a 3DM linestring\. 

```
SELECT ST_NDims(ST_GeomFromText('LINESTRING M(0 0 4,1 1 4,2 2 4,0 0 4)'));
```

```
st_ndims
-------------
 3
```

The following SQL returns the number of dimensions of a 4D linestring\. 

```
SELECT ST_NDims(ST_GeomFromText('LINESTRING ZM(0 0 3 4,1 1 3 4,2 2 3 4,0 0 3 4)'));
```

```
st_ndims
-------------
 4
```