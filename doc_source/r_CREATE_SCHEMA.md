# CREATE SCHEMA<a name="r_CREATE_SCHEMA"></a>

Defines a new schema for the current database\.

## Syntax<a name="r_CREATE_SCHEMA-synopsis"></a>

```
CREATE SCHEMA [ IF NOT EXISTS ] schema_name [ AUTHORIZATION username ] [ schema_element [ ... ] ]

CREATE SCHEMA AUTHORIZATION username [ schema_element [ ... ] ]
```

## Parameters<a name="r_CREATE_SCHEMA-parameters"></a>

 IF NOT EXISTS   
Clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if CREATE SCHEMA tries to create a schema that already exists\.

 *schema\_name*   
Name of the new schema\. The schema name can't be `PUBLIC`\. For more information about valid names, see [Names and Identifiers](r_names.md)\.  
The list of schemas in the [search\_path](r_search_path.md) configuration parameter determines the precedence of identically named objects when they are referenced without schema names\.

AUTHORIZATION   
Clause that gives ownership to a specified user\.

 *username*   
Name of the schema owner\.

 *schema\_element*   
Definition for one or more objects to be created within the schema\.

## Limits<a name="w4aac41b9c73b9"></a>

Amazon Redshift enforces the following limits for schemas\.
+ There is a maximum of 9900 schemas per database\.

## Examples<a name="r_CREATE_SCHEMA-examples"></a>

The following example creates a schema named US\_SALES and gives ownership to the user DWUSER:

```
create schema us_sales authorization dwuser;
```

To view the new schema, query the PG\_NAMESPACE catalog table as shown following:

```
select nspname as schema, usename as owner
from pg_namespace, pg_user
where pg_namespace.nspowner = pg_user.usesysid
and pg_user.usename ='dwuser';

   name   |  owner
----------+----------
 us_sales | dwuser
(1 row)
```

The following example either creates the US\_SALES schema, or does nothing and returns a message if it already exists:

```
create schema if not exists us_sales;
```