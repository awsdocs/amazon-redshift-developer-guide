# ST\_X<a name="ST_X-function"></a>

ST\_X returns the first coordinate of an input point\. 

## Syntax<a name="ST_X-function-syntax"></a>

```
ST_X(point)
```

## Arguments<a name="ST_X-function-arguments"></a>

 *point*   
A `POINT` value of data type `GEOMETRY`\. 

## Return type<a name="ST_X-function-return"></a>

`DOUBLE PRECISION` value of the first coordinate\.

If *point* is null, then null is returned\. 

If *point* is not a `POINT`, then an error is returned\. 

## Examples<a name="ST_X-function-examples"></a>

The following SQL returns the first coordinate of a point\. 

```
SELECT ST_X(ST_Point(1,2));
```

```
st_x
-----------
 1.0
```