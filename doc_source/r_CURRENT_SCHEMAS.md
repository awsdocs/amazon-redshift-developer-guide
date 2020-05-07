# CURRENT\_SCHEMAS<a name="r_CURRENT_SCHEMAS"></a>

Returns an array of the names of any schemas in the current search path\. The current search path is defined in the search\_path parameter\. 

## Syntax<a name="r_CURRENT_SCHEMAS-synopsis"></a>

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

```
current_schemas(include_implicit)
```

## Argument<a name="r_CURRENT_SCHEMAS-argument"></a>

 *include\_implicit*   
If true, specifies that the search path should include any implicitly included system schemas\. Valid values are `true` and `false`\. Typically, if `true`, this parameter returns the `pg_catalog` schema in addition to the current schema\. 

## Return type<a name="r_CURRENT_SCHEMAS-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Examples<a name="r_CURRENT_SCHEMAS-examples"></a>

The following example returns the names of the schemas in the current search path, not including implicitly included system schemas: 

```
select current_schemas(false);

current_schemas
-----------------
{public}
(1 row)
```

The following example returns the names of the schemas in the current search path, including implicitly included system schemas: 

```
select current_schemas(true);

current_schemas
---------------------
{pg_catalog,public}
(1 row)
```