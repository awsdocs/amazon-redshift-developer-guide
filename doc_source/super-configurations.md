# SUPER configurations<a name="super-configurations"></a>

Note the following considerations of SUPER configurations when you use Amazon Redshift SUPER data type and PartiQL\.

## Lax and strict modes for SUPER<a name="lax-strict-modes"></a>

When you query SUPER data, the path expression may not match the actual SUPER data structure\. If you try to access a non\-existent member of an object or element of an array, Amazon Redshift returns a NULL value if your query is run in the default lax mode\. If you run your query in the strict mode, Amazon Redshift returns an error\. The following session parameters can be set to set the lax mode on or off\.

The following example uses session parameters to enable lax mode\.

```
SET navigate_super_null_on_error=ON;  --default lax mode for navigation

SET cast_super_null_on_error=ON;  --default lax mode for casting

SET parse_super_null_on_error=OFF;  --default strict mode for ingestion
```

## Accessing JSON fields with uppercase and mixedcase field names or attributes<a name="upper-mixed-case"></a>

When your JSON attribute names are in uppercase or mixedcase, you must be able to navigate SUPER type structures in a case sensitive way\. To do that, you can configure `enable_case_sensitive_identifier` to TRUE and wrap the uppercase and mixedcase attribute names with double quotation marks\. You can also configure `enable_case_sensitive_super_attribute` to TRUE\. In this case, you can use uppercase and mixedcase attribute names in your queries without wrapping them in double quotation marks\.

The following example illustrates how to set `enable_case_sensitive_identifier` to query data\.

```
SET enable_case_sensitive_identifier to TRUE;
 
-- Accessing JSON attribute names with uppercase and mixedcase names
SELECT json_table.data."ITEMS"."Name",
       json_table.data."price"
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 Name | price
------+-------
 "TV" | 345
(1 row)
 
RESET enable_case_sensitive_identifier;
 
-- After resetting the above configuration, the following query accessing JSON attribute names with uppercase and mixedcase names should return null (if in lax mode).
SELECT json_table.data."ITEMS"."Name",
       json_table.data."price"
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 name | price 
------+-------
      | 345
(1 row)
```

The following example illustrates how to set `enable_case_sensitive_super_attribute` to query data\.

```
SET enable_case_sensitive_super_attribute to TRUE;
 -- Accessing JSON attribute names with uppercase and mixedcase names
 
SELECT json_table.data.ITEMS.Name,
       json_table.data.price 
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 name | price
 -----+-------
 "TV" | 345
(1 row)

 RESET enable_case_sensitive_super_attribute;
 
 -- After resetting enable_case_sensitive_super_attribute, the query now returns NULL for ITEMS.Name (if in lax mode).
 
SELECT json_table.data.ITEMS.Name,
       json_table.data.price 
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 name | price
 -----+-------
      | 345
(1 row)
```

## Parsing options for SUPER<a name="parsing-options-super"></a>

When you use the JSON\_PARSE function to parse JSON strings into SUPER values, certain restrictions apply: 
+ The same attribute name cannot appear in the same object, but can appear in a nested object\. The `json_parse_dedup_attributes` configuration option allows JSON\_PARSE to keep only the last occurrence of duplicate attributes instead of returning an error\. 
+ String values cannot exceed the system max varchar size of 65535 bytes\. The `json_parse_truncate_strings` configuration option allows JSON\_PARSE\(\) to automatically truncate strings that are longer than this limit without returning an error\. This behavior affects string values only and not attribute names\.

For more information about the JSON\_PARSE function, see [JSON\_PARSE function](JSON_PARSE.md)\.

The following example shows how to set the `json_parse_dedup_attributes` configuration option to the default behavior of returning an error for duplicate attributes\.

```
SET json_parse_dedup_attributes=OFF;  --default behavior of returning error instead of de-duplicating attributes
```

The following example shows how to set the `json_parse_truncate_strings` configuration option for the default behavior of returning an error for strings that are longer than this limit\.

```
SET json_parse_truncate_strings=OFF;  --default behavior of returning error instead of truncating strings
```