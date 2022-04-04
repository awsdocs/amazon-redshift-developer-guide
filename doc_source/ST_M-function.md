# ST\_M<a name="ST_M-function"></a>

ST\_M returns the `m` coordinate of an input point\. 

## Syntax<a name="ST_M-function-syntax"></a>

```
ST_M(point)
```

## Arguments<a name="ST_M-function-arguments"></a>

 *point*   
A `POINT` value of data type `GEOMETRY`\. 

## Return type<a name="ST_M-function-return"></a>

`DOUBLE PRECISION` value of the `m` coordinate\.

If *point* is null, then null is returned\. 

If *point* is a 2D or 3DZ point, then null is returned\. 

If *point* is the empty point, then null is returned\. 

If *point* is not a `POINT`, then an error is returned\. 

## Examples<a name="ST_M-function-examples"></a>

The following SQL returns the `m` coordinate of a point in a 3DM geometry\. 

```
SELECT ST_M(ST_GeomFromEWKT('POINT M (1 2 3)'));
```

```
st_m
-----------
 3
```

The following SQL returns the `m` coordinate of a point in a 4D geometry\. 

```
SELECT ST_M(ST_GeomFromEWKT('POINT ZM (1 2 3 4)'));
```

```
st_m
-----------
 4
```