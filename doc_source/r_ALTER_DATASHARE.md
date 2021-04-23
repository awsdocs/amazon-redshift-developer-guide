# ALTER DATASHARE<a name="r_ALTER_DATASHARE"></a>

Changes the definition of a datashare\. You can add objects or remove objects using ALTER DATASHARE\. The owner of the datashare with the required permissions on the datashare objects to be added or removed can alter the datashare\.

## Syntax<a name="r_ALTER_DATASHARE-synopsis"></a>

```
ALTER DATASHARE datashare_name { ADD | REMOVE } { 
TABLE schema.table [, ...] 
| SCHEMA schema [, ...] 
| FUNCTION schema.sql_udf [, ...] 
| ALL TABLES IN SCHEMA schema [, ...]  
| ALL FUNCTIONS IN SCHEMA schema [, ...] }
[ SET PUBLICACCESSIBLE [=] TRUE | FALSE ]
```

## Parameters<a name="r_ALTER_DATASHARE-parameters"></a>

*datashare\_name*  
The name of the datashare to be altered\. 

ADD \| REMOVE  
A clause that specifies whether to add objects to or remove objects from the datashare\.

TABLE *schema*\.*table* \[, \.\.\.\]  
The name of the table or view in the specified schema to add to the datashare\.

SCHEMA *schema* \[, \.\.\.\]   
The name of the schema to add to the datashare\.

FUNCTION *schema*\.*sql\_udf* \[, \.\.\.\]  
The name of the user\-defined SQL function to add to the datashare\.

ALL TABLES IN SCHEMA *schema* \[, \.\.\.\]  
A clause that specifies adding all tables in the specified schema to the datashare\.

ALL FUNCTIONS IN SCHEMA *schema* \[, \.\.\.\] \}  
A clause that specifies adding all functions in the specified schema to the datashare\.

\[ SET PUBLICACCESSIBLE \[=\] TRUE \| FALSE \]  
A clause that specifies whether a datashare can be shared to clusters that are publicly accessible\.

## Usage notes<a name="r_ALTER_DATASHARE_usage"></a>

The following users can alter a datashare:
+ A superuser
+ The owner of the datashare
+ Users who have ALTER or ALL privilege on the datashare

To add specific objects to a datashare, these users must have the privilege on the objects\. Users should be the owners of objects or have SELECT, USAGE or ALL privileges on the objects\.

You can share schemas, tables, regular views, late\-binding views, materialized views, and SQL user\-defined functions \(UDFs\)\. Add a schema to the datashare first before adding other objects in the schema\. When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\. 

## Examples<a name="r_ALTER_DATASHARE_examples"></a>

The following example adds the public\.tickit\_sales\_redshift table to the datashare SalesShare\.

```
ALTER DATASHARE SalesShare ADD TABLE public.tickit_sales_redshift;
```

The following example adds all tables to the datashare SalesShare\.

```
ALTER DATASHARE SalesShare ADD ALL TABLES IN SCHEMA PUBLIC;
```

The following example removes the public\.tickit\_sales\_redshift table from the datashare SalesShare\.

```
ALTER DATASHARE SalesShare REMOVE TABLE public.tickit_sales_redshift;
```