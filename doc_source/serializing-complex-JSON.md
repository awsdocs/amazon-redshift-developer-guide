# Serializing complex nested JSON<a name="serializing-complex-JSON"></a>

An alternate to methods demonstrated in this tutorial is to query top\-level nested collection columns as serialized JSON\. You can use the serialization to inspect, convert, and ingest nested data as JSON with Redshift Spectrum\. This method is supported for ORC, JSON, Ion, and Parquet formats\. Use the session configuration parameter `json_serialization_enable` to configure the serialization behavior\. When set, complex JSON data types are serialized to VARCHAR\(65535\)\. The nested JSON can be accessed with [JSON functions](json-functions.md)\. For more information, see [json\_serialization\_enable](r_json_serialization_enable.md)\.

For example, without setting `json_serialization_enable`, the following queries that access nested columns directly fail\. 

```
SELECT * FROM spectrum.customers LIMIT 1;

=> ERROR:  Nested tables do not support '*' in the SELECT clause.

SELECT name FROM spectrum.customers LIMIT 1;

=> ERROR:  column "name" does not exist in customers
```

Setting `json_serialization_enable` enables querying top\-level collections directly\. 

```
SET json_serialization_enable TO true;

SELECT * FROM spectrum.customers order by id LIMIT 1;

id | name                                 | phones         | orders
---+--------------------------------------+----------------+----------------------------------------------------------------------------------------------------------------------
1  | {"given": "John", "family": "Smith"} | ["123-457789"] | [{"shipdate": "2018-03-01T11:59:59.000Z", "price": 100.50}, {"shipdate": "2018-03-01T09:10:00.000Z", "price": 99.12}]          
 
SELECT name FROM spectrum.customers order by id LIMIT 1;

name
---------
{"given": "John", "family": "Smith"}
```

Consider the following items when serializing nested JSON\.
+ When collection columns are serialized as VARCHAR\(65535\), their nested subfields can't be accessed directly as part of the query syntax \(for example, in the filter clause\)\. However, JSON functions can be used to access nested JSON\. 
+ The following specialized representations are not supported: 
  + ORC unions
  + ORC maps with complex type keys
  + Ion datagrams
  + Ion SEXP
+ Timestamps are returned as ISO serialized strings\.
+ Primitive map keys are promoted to string \(for example, `1` to `"1"`\)\.
+ Top\-level null values are serialized as NULLs\.
+ If the serialization overflows the maximum VARCHAR size of 65535, the cell is set to NULL\.

## Serializing complex types containing JSON strings<a name="serializing-complex-JSON-strings"></a>

By default, string values contained in nested collections are serialized as escaped JSON strings\. Escaping might be undesirable when the strings are valid JSON\. Instead you might want to write nested subelements or fields that are VARCHAR directly as JSON\. Enable this behavior with the `json_serialization_parse_nested_strings` session\-level configuration\. When both `json_serialization_enable` and `json_serialization_parse_nested_strings` are set, valid JSON values are serialized inline without escape characters\. When the value is not valid JSON, it is escaped as if the `json_serialization_parse_nested_strings` configuration value was not set\. For more information, see [json\_serialization\_parse\_nested\_strings](r_json_serialization_parse_nested_strings.md)\.

For example, assume the data from the previous example contained JSON as a `structs` complex type in the `name` VARCHAR\(20\) field: 

```
name
---------
{"given": "{\"first\":\"John\",\"middle\":\"James\"}", "family": "Smith"}
```

When `json_serialization_parse_nested_strings` is set, the `name` column is serialized as follows: 

```
SET json_serialization_enable TO true;
SET json_serialization_parse_nested_strings TO true;
SELECT name FROM spectrum.customers order by id LIMIT 1;

name
---------
{"given": {"first":"John","middle":"James"}, "family": "Smith"}
```

Instead of being escaped like this:

```
SET json_serialization_enable TO true;
SELECT name FROM spectrum.customers order by id LIMIT 1;

name
---------
{"given": "{\"first\":\"John\",\"middle\":\"James\"}", "family": "Smith"}
```