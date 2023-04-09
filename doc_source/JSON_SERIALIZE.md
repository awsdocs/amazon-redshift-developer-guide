# JSON\_SERIALIZE function<a name="JSON_SERIALIZE"></a>

The JSON\_SERIALIZE function serializes a SUPER expression into textual JSON representation to follow RFC 8259\. For more information on that RFC, see [The JavaScript Object Notation \(JSON\) Data Interchange Format](https://tools.ietf.org/html/rfc8259)\.

The SUPER size limit is approximately the same as the block limit, and the varchar limit is smaller than the SUPER size limit\. Therefore, the JSON\_SERIALIZE function returns an error when the JSON format exceeds the varchar limit of the system\. To check the size of a SUPER expression, see the [JSON\_SIZE](r_json_size.md) function\.

## Syntax<a name="JSON_SERIALIZE-synopsis"></a>

```
JSON_SERIALIZE(super_expression)
```

## Arguments<a name="JSON_SERIALIZE-arguments"></a>

 *super\_expression*  
A super expression or column\.

## Return type<a name="JSON_SERIALIZE-return"></a>

varchar

## Example<a name="JSON_SERIALIZE-examples"></a>

The following example serializes a SUPER value to a string\.

```
 SELECT JSON_SERIALIZE(JSON_PARSE('[10001,10002,"abc"]'));
   json_serialize
---------------------
 [10001,10002,"abc"]
(1 row)
```