# DROP SCHEMA<a name="r_DROP_SCHEMA"></a>

Deletes a schema\. This command is not reversible\.

## Syntax<a name="r_DROP_SCHEMA-synopsis"></a>

```
DROP SCHEMA [ IF EXISTS ] name [, ...] [ CASCADE | RESTRICT ]
```

## Parameters<a name="r_DROP_SCHEMA-parameters"></a>

IF EXISTS  
Clause that indicates that if the specified schema doesn’t exist, the command should make no changes and return a message that the schema doesn't exist, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if DROP SCHEMA runs against a nonexistent schema\.

 *name*   
Name of the schema to drop\.

CASCADE  
Keyword that indicates to automatically drop all objects in the schema, such as tables and functions\.

RESTRICT  
Keyword that indicates not to drop the schema if it contains any objects\. This action is the default\.

## Example<a name="r_DROP_SCHEMA-example"></a>

The following example deletes a schema named S\_SALES\. This example uses RESTRICT as a safety mechanism so that the schema will not be deleted if it contains any objects\. In this case, you would need to delete the schema objects before deleting the schema:

```
drop schema s_sales restrict;
```

The following example deletes a schema named S\_SALES and all objects that depend on that schema:

```
drop schema s_sales cascade;
```

The following example either drops the S\_SALES schema if it exists, or does nothing and returns a message if it does not:

```
drop schema if exists s_sales;
```