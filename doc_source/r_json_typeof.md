# JSON\_TYPEOF function<a name="r_json_typeof"></a>

The JSON\_TYPEOF scalar function returns a VARCHAR with values boolean, number, string, object, array, or null, depending on the dynamic type of the SUPER value\. 

## Syntax<a name="r_json_typeof-synopsis"></a>

```
JSON_TYPEOF (super_expression)
```

## Arguments<a name="r_json_typeof-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_json_typeof-returns"></a>

Varchar

## Examples<a name="r_json_typeof_example"></a>

The following queries show a JSON\_TYPEOF function\.

```
SELECT JSON_TYPEOF(ARRAY(1,2));

 json_typeof
---------------
 array
(1 row)
```

```
 SELECT JSON_TYPEOF(JSON_PARSE('{"name":"Joe"}'));

 json_typeof
---------------
 object
(1 row)
```