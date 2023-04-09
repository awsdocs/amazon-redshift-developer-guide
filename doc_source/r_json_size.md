# JSON\_SIZE function<a name="r_json_size"></a>

The JSON\_SIZE function returns the number of bytes in the given SUPER expression when serialized into a string\. 

## Syntax<a name="r_json_size-synopsis"></a>

```
JSON_SIZE(super_expression)
```

## Arguments<a name="r_json_size-arguments"></a>

*super\_expression*  
A SUPER constant or expression\.

## Returns<a name="r_json_size-returns"></a>

The JSON\_SIZE function returns an integer indicating the number of bytes in the input string\. This value is different from the number of characters\. For example, the UTF\-8 character ⬤, a black dot, is 3 bytes in size\.

## Usage Notes<a name="r_json_size-usage_notes"></a>

JSON\_SIZE\(x\) is functionally identical to OCTET\_LENGTH\(JSON\_SERIALIZE\)\. However, note that JSON\_SERIALIZE returns an error when the provided SUPER expression would exceed the VARCHAR limit of the system when serialized\. JSON\_SIZE does not have this limitation\.

## Examples<a name="r_json_size_example"></a>

The following example returns the length of a SUPER value serialized to a string\.

```
SELECT JSON_SIZE(JSON_PARSE('[10001,10002,"⬤"]'));
  json_size
--------------
  19         
(1 row)
```

The provided SUPER expression is 17 characters long, but ⬤ is a 3\-byte character, so JSON\_SIZE returns 19\.