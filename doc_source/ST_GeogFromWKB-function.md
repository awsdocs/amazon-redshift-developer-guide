# ST\_GeogFromWKB<a name="ST_GeogFromWKB-function"></a>

ST\_GeogFromWKB constructs a geography object from a hexadecimal well\-known binary \(WKB\) representation of an input geography\. 

## Syntax<a name="ST_GeogFromWKB-function-syntax"></a>

```
ST_GeogFromWKB(wkb_string)
```

## Arguments<a name="ST_GeogFromWKB-function-arguments"></a>

 *wkb\_string*   
A value of data type `VARCHAR` that is a hexadecimal WKB representation of a geography\.

## Return type<a name="ST_GeogFromWKB-function-return"></a>

`GEOGRAPHY`

If the SRID value is provided it is set to the provided value\. If SRID is not provided, it is set to `4326`\. 

If *wkb\_string* is null, then null is returned\. 

If *wkb\_string* is not valid, then an error is returned\. 

## Examples<a name="ST_GeogFromWKB-function-examples"></a>

The following SQL constructs a geography from a hexadecimal WKB value\. 

```
SELECT ST_AsEWKT(ST_GeogFromWKB('01030000000100000005000000000000000000000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000F03F000000000000F03F000000000000000000000000000000000000000000000000'));
```

```
 st_asewkt
------------------------------------------
 SRID=4326;POLYGON((0 0,0 1,1 1,1 0,0 0))
```