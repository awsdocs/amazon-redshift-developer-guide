# ST\_MakePoint<a name="ST_MakePoint-function"></a>

ST\_MakePoint returns a point geometry whose coordinate values are the input values\. 

## Syntax<a name="ST_MakePoint-function-syntax"></a>

```
ST_MakePoint(x, y)
```

## Arguments<a name="ST_MakePoint-function-arguments"></a>

 *x*   
A value of data type `DOUBLE PRECISION` representing the first coordinate\. 

 *y*   
A value of data type `DOUBLE PRECISION` representing the second coordinate\. 

## Return type<a name="ST_MakePoint-function-return"></a>

`GEOMETRY` of subtype `POINT`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is set to 0\. 

If *x* or *y* is null, then null is returned\. 

## Examples<a name="ST_MakePoint-function-examples"></a>

The following SQL returns a `GEOMETRY` type of subtype `POINT` with the provided coordinates\. 

```
SELECT ST_AsText(ST_MakePoint(1,3));
```

```
st_astext
-----------
 POINT(1 3)
```