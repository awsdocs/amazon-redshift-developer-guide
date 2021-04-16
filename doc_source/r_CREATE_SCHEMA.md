# CREATE SCHEMA<a name="r_CREATE_SCHEMA"></a>

Defines a new schema for the current database\.

## Syntax<a name="r_CREATE_SCHEMA-synopsis"></a>

```
CREATE SCHEMA [ IF NOT EXISTS ] schema_name [ AUTHORIZATION username ] 
           [ QUOTA {quota [MB | GB | TB] | UNLIMITED} ] [ schema_element [ ... ] 

CREATE SCHEMA AUTHORIZATION username[ QUOTA {quota [MB | GB | TB] | UNLIMITED} ] [ schema_element [ ... ] ]
```

## Parameters<a name="r_CREATE_SCHEMA-parameters"></a>

 IF NOT EXISTS   
Clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if CREATE SCHEMA tries to create a schema that already exists\.

 *schema\_name*   
Name of the new schema\. The schema name can't be `PUBLIC`\. For more information about valid names, see [Names and identifiers](r_names.md)\.  
The list of schemas in the [search\_path](r_search_path.md) configuration parameter determines the precedence of identically named objects when they are referenced without schema names\.

AUTHORIZATION   
Clause that gives ownership to a specified user\.

 *username*   
Name of the schema owner\.

 *schema\_element*   
Definition for one or more objects to be created within the schema\.

QUOTA  
The maximum amount of disk space that the specified schema can use\. This space is the collective disk usage\. It includes all permanent tables, materialized views under the specified schema, and duplicate copies of all tables with ALL distribution on each compute node\. The schema quota doesn't take into account temporary tables created as part of a temporary namespace or schema\.   
To view the configured schema quotas, see [SVV\_SCHEMA\_QUOTA\_STATE](r_SVV_SCHEMA_QUOTA_STATE.md)\.  
To view the records where schema quotas were exceeded, see [STL\_SCHEMA\_QUOTA\_VIOLATIONS](r_STL_SCHEMA_QUOTA_VIOLATIONS.md)\.  
Amazon Redshift converts the selected value to megabytes\. Gigabytes is the default unit of measurement when you don't specify a value\.  
You must be a database superuser to set and change a schema quota\. A user that is not a superuser but that has CREATE SCHEMA permission can create a schema with a defined quota\. When you create a schema without defining a quota, the schema has an unlimited quota\. When you set the quota below the current value used by the schema, Amazon Redshift doesn't allow further ingestion until you free disk space\. A DELETE statement deletes data from a table and disk space is freed up only when VACUUM runs\.   
Amazon Redshift checks each transaction for quota violations before committing the transaction\. Amazon Redshift checks the size \(the disk space used by all tables in a schema\) of each modified schema against the set quota\. Because the quota violation check occurs at the end of a transaction, the size limit can exceed the quota temporarily within a transaction before it's committed\. When a transaction exceeds the quota, Amazon Redshift aborts the transaction, prohibits subsequent ingestions, and reverts all the changes until you free disk space\. Due to background VACUUM and internal cleanup, it is possible that a schema isn't full by the time that you check the schema after an aborted transaction\.   
As an exception, Amazon Redshift disregards the quota violation and commits transactions in certain cases\. Amazon Redshift does this for transactions that consist solely of one or more of the following statements where there isn't an INSERT or COPY ingestion statement in the same schema:  
+ DELETE
+ TRUNCATE
+ VACUUM
+ DROP TABLE
+ ALTER TABLE APPEND only when moving data from the full schema to another non\-full schema

 *UNLIMITED*   
Amazon Redshift imposes no limit to the growth of the total size of the schema\.

## Limits<a name="r_CREATE_SCHEMA-limit"></a>

Amazon Redshift enforces the following limits for schemas\.
+ There is a maximum of 9900 schemas per database\.

## Examples<a name="r_CREATE_SCHEMA-examples"></a>

The following example creates a schema named US\_SALES and gives ownership to the user DWUSER\.

```
create schema us_sales authorization dwuser;
```

The following example creates a schema named US\_SALES, gives ownership to the user DWUSER, and sets the quota to 50 GB\.

```
create schema us_sales authorization dwuser QUOTA 50 GB;
```

To view the new schema, query the PG\_NAMESPACE catalog table as shown following\.

```
select nspname as schema, usename as owner
from pg_namespace, pg_user
where pg_namespace.nspowner = pg_user.usesysid
and pg_user.usename ='dwuser';

   schema |  owner
----------+----------
 us_sales | dwuser
(1 row)
```

The following example either creates the US\_SALES schema, or does nothing and returns a message if it already exists\.

```
create schema if not exists us_sales;
```