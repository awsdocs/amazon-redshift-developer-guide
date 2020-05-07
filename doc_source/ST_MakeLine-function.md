# ST\_MakeLine<a name="ST_MakeLine-function"></a>

ST\_MakeLine creates a linestring from the input geometries\. 

## Syntax<a name="ST_MakeLine-function-syntax"></a>

```
ST_MakeLine(geom1, geom2)
```

## Arguments<a name="ST_MakeLine-function-arguments"></a>

 *geom1*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`, `LINESTRING`, or `MULTIPOINT`\. 

 *geom2*   
A value of data type `GEOMETRY` or an expression that evaluates to a `GEOMETRY` type\. The subtype must be `POINT`, `LINESTRING`, or `MULTIPOINT`\. 

## Return type<a name="ST_MakeLine-function-return"></a>

`GEOMETRY` of subtype `LINESTRING`\. 

The spatial reference system identifier \(SRID\) value of the returned geometry is the SRID value of the input geometries\. 

If *geom1* or *geom2* is null, then null is returned\. 

If *geom1* and *geom2* have different SRID values, then an error is returned\. 

If *geom1* or *geom2* is not a `POINT`, `LINESTRING`, or `MULTIPOINT`, then an error is returned\. 

## Examples<a name="ST_MakeLine-function-examples"></a>

The following SQL constructs a linestring from two input linestrings\. 

```
SELECT ST_MakeLine(ST_GeomFromText('LINESTRING(77.29 29.07,77.42 29.26,77.27 29.31,77.29 29.07)'), ST_GeomFromText('LINESTRING(88.29 39.07,88.42 39.26,88.27 39.31,88.29 39.07)'));
```

```
st_makeline
-----------
 010200000008000000C3F5285C8F52534052B81E85EB113D407B14AE47E15A5340C3F5285C8F423D40E17A14AE475153408FC2F5285C4F3D40C3F5285C8F52534052B81E85EB113D40C3F5285C8F125640295C8FC2F58843407B14AE47E11A5640E17A14AE47A14340E17A14AE4711564048E17A14AEA74340C3F5285C8F125640295C8FC2F5884340
```