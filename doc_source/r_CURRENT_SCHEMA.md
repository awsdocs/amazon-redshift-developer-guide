# CURRENT\_SCHEMA<a name="r_CURRENT_SCHEMA"></a>

Returns the name of the schema at the front of the search path\. This schema will be used for any tables or other named objects that are created without specifying a target schema\. 

## Syntax<a name="r_CURRENT_SCHEMA-synopsis"></a>

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

```
current_schema()
```

## Return type<a name="r_CURRENT_SCHEMA-return-type"></a>

CURRENT\_SCHEMA returns a CHAR or VARCHAR string\. 

## Examples<a name="r_CURRENT_SCHEMA-examples"></a>

The following query returns the current schema: 

```
select current_schema();

current_schema
----------------
public
(1 row)
```