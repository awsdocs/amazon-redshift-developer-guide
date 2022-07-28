# JSON\_PARSE function<a name="JSON_PARSE"></a>

The JSON\_PARSE function parses data in JSON format and converts it into the SUPER representation\. 

To ingest into SUPER data type using the INSERT or UPDATE command, use the JSON\_PARSE function\. When you use JSON\_PARSE\(\) to parse JSON strings into SUPER values, certain restrictions apply\. For additional information, see [Parsing options for SUPER](super-configurations.md#parsing-options-super)\.

## Syntax<a name="JSON_PARSE-synopsis"></a>

```
JSON_PARSE(json_string)
```

## Arguments<a name="JSON_PARSE-arguments"></a>

 *json\_string*  
An expression that returns serialized JSON as a varbyte or varchar type\. 

## Return type<a name="JSON_PARSE-return"></a>

SUPER

## Example<a name="JSON_PARSE-examples"></a>

The following example is an example of the JSON\_PARSE function\.

```
SELECT JSON_PARSE('[10001,10002,"abc"]');
     json_parse
--------------------------
 [10001,10002,"abc"]
(1 row)
```

```
SELECT JSON_TYPEOF(JSON_PARSE('[10001,10002,"abc"]'));
 json_typeof
----------------
 array
(1 row)
```