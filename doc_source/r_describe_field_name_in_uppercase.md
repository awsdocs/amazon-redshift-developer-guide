# describe\_field\_name\_in\_uppercase<a name="r_describe_field_name_in_uppercase"></a>

## Values \(default in bold\)<a name="r_describe_field_name_in_uppercase-values"></a>

**off \(false\)**, on \(true\)

## Description<a name="description"></a>

Specifies whether column names returned by SELECT statements are uppercase or lowercase\. If on, column names are returned in uppercase\. If off, column names are returned in lowercase\. Amazon Redshift stores column names in lowercase regardless of the setting for `describe_field_name_in_uppercase`\.

## Example<a name="example"></a>

```
set describe_field_name_in_uppercase to on;
            
show describe_field_name_in_uppercase;

DESCRIBE_FIELD_NAME_IN_UPPERCASE
--------------------------------
on
```