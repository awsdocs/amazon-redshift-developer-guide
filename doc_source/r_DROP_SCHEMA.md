# DROP SCHEMA<a name="r_DROP_SCHEMA"></a>

Deletes a schema\. For an external schema, you can also drop the external database associated with the schema\. This command isn't reversible\.

## Syntax<a name="r_DROP_SCHEMA-synopsis"></a>

```
DROP SCHEMA [ IF EXISTS ] name [, ...] 
[ DROP EXTERNAL DATABASE ]
[ CASCADE | RESTRICT ]
```

## Parameters<a name="r_DROP_SCHEMA-parameters"></a>

IF EXISTS  
Clause that indicates that if the specified schema doesn’t exist, the command should make no changes and return a message that the schema doesn't exist, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if DROP SCHEMA runs against a nonexistent schema\.

 *name*   
Names of the schemas to drop\. You can specify multiple schema names separated by commas\.

 DROP EXTERNAL DATABASE   
Clause that indicates that if an external schema is dropped, drop the external database associated with the external schema, if one exists\. If no external database exists, the command returns a message stating that no external database exists\. If multiple external schemas are dropped, all databases associated with the specified schemas are dropped\.   
If an external database contains dependent objects such as tables, include the CASCADE option to drop the dependent objects as well\.   
When you drop an external database, the database is also dropped for any other external schemas associated with the database\. Tables defined in other external schemas using the database are also dropped\.   
DROP EXTERNAL DATABASE doesn't support external databases stored in a HIVE metastore\. 

CASCADE  
Keyword that indicates to automatically drop all objects in the schema\. If DROP EXTERNAL DATABASE is specified, all objects in the external database are also dropped\.

RESTRICT  
Keyword that indicates not to drop a schema or external database if it contains any objects\. This action is the default\.

## Example<a name="r_DROP_SCHEMA-example"></a>

The following example deletes a schema named S\_SALES\. This example uses RESTRICT as a safety mechanism so that the schema isn't deleted if it contains any objects\. In this case, you need to delete the schema objects before deleting the schema\.

```
drop schema s_sales restrict;
```

The following example deletes a schema named S\_SALES and all objects that depend on that schema\.

```
drop schema s_sales cascade;
```

The following example either drops the S\_SALES schema if it exists, or does nothing and returns a message if it doesn't\.

```
drop schema if exists s_sales;
```

The following example deletes an external schema named S\_SPECTRUM and the external database associated with it\. This example uses RESTRICT so that the schema and database aren't deleted if they contain any objects\. In this case, you need to delete the dependent objects before deleting the schema and the database\.

```
drop schema s_spectrum drop external database restrict;
```

The following example deletes multiple schemas and the external databases associated with them, along with any dependent objects\. 

```
drop schema s_sales, s_profit, s_revenue drop external database cascade;
```