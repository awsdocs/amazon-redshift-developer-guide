# JSON\_SERIALIZE\_TO\_VARBYTE function<a name="JSON_SERIALIZE_TO_VARBYTE"></a>

The JSON\_SERIALIZE\_TO\_VARBYTE function converts a SUPER value to a JSON string similar to JSON\_SERIALIZE\(\), but stored in a VARBYTE value instead\.

## Syntax<a name="JSON_SERIALIZE_TO_VARBYTE-synopsis"></a>

```
JSON_SERIALIZE_TO_VARBYTE(super_expression)
```

## Arguments<a name="JSON_SERIALIZE_TO_VARBYTE-arguments"></a>

 *super\_expression*  
A super expression or column\.

## Return type<a name="JSON_SERIALIZE_TO_VARBYTE-return"></a>

varbyte

## Example<a name="JSON_SERIALIZE_TO_VARBYTE-examples"></a>

The following example serializes a SUPER value and returns the result in VARBYTE format\.

```
SELECT JSON_SERIALIZE_TO_VARBYTE(JSON_PARSE('[10001,10002,"abc"]'));
```

```
       json_serialize_to_varbyte
----------------------------------------
 5b31303030312c31303030322c22616263225d
```

The following example serializes a SUPER value and casts the result to VARCHAR format\.

```
SELECT JSON_SERIALIZE_TO_VARBYTE(JSON_PARSE('[10001,10002,"abc"]'))::VARCHAR;
```

```
  json_serialize_to_varbyte
---------------------------
 [10001,10002,"abc"]
```