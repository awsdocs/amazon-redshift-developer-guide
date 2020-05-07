# ST\_Point<a name="ST_Point-function"></a>

ST\_Point returns a point geometry from the input coordinate values\. 

## Syntax<a name="ST_Point-function-syntax"></a>

```
ST_Point(x, y)
```

## Arguments<a name="ST_Point-function-arguments"></a>

 *x*   
A value of data type `DOUBLE PRECISION` that represents a first coordinate\. 

 *y*   
A value of data type `DOUBLE PRECISION` that represents a second coordinate\. 

## Return type<a name="ST_Point-function-return"></a>

`GEOMETRY` of subtype `POINT`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is set to 0\. 

If *x* or *y* is null, then null is returned\. 

## Examples<a name="ST_Point-function-examples"></a>

The following SQL constructs a point geometry from the input coordinates\. 

```
SELECT ST_AsText(ST_Point(5.0, 7.0));
```

```
st_astext
-------------
POINT(5 7)
```