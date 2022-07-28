# CAN\_JSON\_PARSE function<a name="CAN_JSON_PARSE"></a>

The CAN\_JSON\_PARSE function parses data in JSON format and returns true if the result can be converted to a SUPER value using the JSON\_PARSE function\.

## Syntax<a name="CAN_JSON_PARSE-synopsis"></a>

```
CAN_JSON_PARSE(json_string)
```

## Arguments<a name="CAN_JSON_PARSE-arguments"></a>

 *json\_string*  
An expression that returns serialized JSON in the varbyte or varchar form\. 

## Return type<a name="CAN_JSON_PARSE-return"></a>

Boolean

## Example<a name="CAN_JSON_PARSE-examples"></a>

The following example is an example of the CAN\_JSON\_PARSE function\.

```
SELECT CAN_JSON_PARSE('[10001,10002,"abc"]');
```

```
can_json_parse
----------------
 t
```