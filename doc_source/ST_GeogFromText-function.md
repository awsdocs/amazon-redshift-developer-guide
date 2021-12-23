# ST\_GeogFromText<a name="ST_GeogFromText-function"></a>

ST\_GeogFromText constructs a geography object from a well\-known text \(WKT\) or extended well\-known text \(EWKT\) representation of an input geography\. 

## Syntax<a name="ST_GeogFromText-function-syntax"></a>

```
ST_GeogFromText(wkt_string)
```

## Arguments<a name="ST_GeogFromText-function-arguments"></a>

 *wkt\_string*   
A value of data type `VARCHAR` that is a WKT or EWKT representation of a geography\.

## Return type<a name="ST_GeogFromText-function-return"></a>

`GEOGRAPHY`

If the SRID value is set to the provided value in the input\. If SRID is not provided, it is set to `4326`\. 

If *wkt\_string* is null, then null is returned\. 

If *wkt\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeogFromText-function-examples"></a>

The following SQL constructs a polygon from a geography object with an SRID value\. 

```
SELECT ST_AsEWKT(ST_GeogFromText('SRID=4324;POLYGON((0 0,0 1,1 1,10 10,1 0,0 0))'));
```

```
  st_asewkt
------------------------------------------------
 SRID=4324;POLYGON((0 0,0 1,1 1,10 10,1 0,0 0))
```

The following SQL constructs a polygon from a geography object\. The SRID value is set to `4326`\.

```
SELECT ST_AsEWKT(ST_GeogFromText('POLYGON((0 0,0 1,1 1,10 10,1 0,0 0))'));
```

```
 st_asewkt
------------------------------------------------
 SRID=4326;POLYGON((0 0,0 1,1 1,10 10,1 0,0 0))
```