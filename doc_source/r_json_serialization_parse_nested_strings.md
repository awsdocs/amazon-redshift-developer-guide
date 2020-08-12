# json\_serialization\_parse\_nested\_strings<a name="r_json_serialization_parse_nested_strings"></a>

## Values \(default in bold\)<a name="r_json_serialization_parse_nested_strings-values"></a>

**false**, true

## Description<a name="description"></a>

A session configuration that modifies the JSON serialization behavior of ORC, JSON, Ion, and Parquet formatted data\. When both `json_serialization_parse_nested_strings` and `json_serialization_enable` are true, string values that are stored in complex types \(such as, maps, structs, or arrays\) are parsed and written inline directly into the result if they are valid JSON\. If `json_serialization_parse_nested_strings` is false, strings within nested complex types are serialized as escaped JSON strings\. For more information, see [Serializing complex types containing JSON strings](serializing-complex-JSON.md#serializing-complex-JSON-strings)\. 