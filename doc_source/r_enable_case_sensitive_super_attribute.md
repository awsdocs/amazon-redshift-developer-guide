# enable\_case\_sensitive\_super\_attribute<a name="r_enable_case_sensitive_super_attribute"></a>

## Values \(default in bold\)<a name="r_enable_case_sensitive_super_attribute-values"></a>

true, **false**

## Description<a name="r_enable_case_sensitive_super_attribute-description"></a>

A configuration value that determines whether navigating SUPER data type structures is case sensitive\. When you set `enable_case_sensitive_super_attribute` to `true`, navigating SUPER type structures is case sensitive\. When you set the value to `false`, navigating SUPER type structures is not case sensitive\. 

When you enclose an attribute name in double quotation marks and set `enable_case_sensitive_identifier` to `true`, case is always preserved, regardless of the setting of the `enable_case_sensitive_super_attribute` configuration option\.

`enable_case_sensitive_super_attribute` only applies to columns with the SUPER data type\. For all other columns, consider using `enable_case_sensitive_identifier` instead\.

For more information on the SUPER data type, see [SUPER type](r_SUPER_type.md) and [Ingesting and querying semistructured data in Amazon Redshift](super-overview.md)\.

## Examples<a name="w189aac71c47b7"></a>

The following example shows the results of selecting SUPER values with `enable_case_sensitive_super_attribute` enabled and with it disabled\.

```
--Create a table with a SUPER column.
CREATE TABLE tbl (col SUPER);
 
--Insert values.
INSERT INTO tbl VALUES (json_parse('{
 "A": "A", "a": "a"
}'));

SET enable_case_sensitive_super_attribute TO ON;

SELECT col.A FROM tbl;
  a 
----- 
 "A"
(1 row)

SELECT col.a FROM tbl;
  a 
----- 
 "a"
(1 row)

SET enable_case_sensitive_super_attribute TO OFF;

SELECT col.A FROM tbl;
  a 
-----
 "a"
(1 row)

SELECT col.a FROM tbl;
  a  
-----
 "a"
(1 row)
```

## Usage Notes<a name="r_enable_case_sensitive_super_attribute-usage-notes"></a>
+  Views and materialized views follow the value of `enable_case_sensitive_super_attribute` at the time of their creation\. Late\-binding views, stored procedures, and user\-defined functions follow the value of `enable_case_sensitive_super_attribute` at the time of querying\. 
+  The column name in statement results is always downcased, regardless of the value of `enable_case_sensitive_super_attribute`\. To make the column name case sensitive as well, enable `enable_case_sensitive_identifier`\. 