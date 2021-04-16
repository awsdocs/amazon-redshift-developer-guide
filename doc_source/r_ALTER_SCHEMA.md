# ALTER SCHEMA<a name="r_ALTER_SCHEMA"></a>

Changes the definition of an existing schema\. Use this command to rename a schema or change the owner of a schema\. 

For example, rename an existing schema to preserve a backup copy of that schema when you plan to create a new version of that schema\. For more information about schemas, see [CREATE SCHEMA](r_CREATE_SCHEMA.md)\. 

To view the configured schema quotas, see [SVV\_SCHEMA\_QUOTA\_STATE](r_SVV_SCHEMA_QUOTA_STATE.md)\.

To view the records where schema quotas were exceeded, see [STL\_SCHEMA\_QUOTA\_VIOLATIONS](r_STL_SCHEMA_QUOTA_VIOLATIONS.md)\.

## Syntax<a name="r_ALTER_SCHEMA-synopsis"></a>

```
ALTER SCHEMA schema_name
{
RENAME TO new_name |
OWNER TO new_owner |
QUOTA { quota [MB | GB | TB] | UNLIMITED }
}
```

## Parameters<a name="r_ALTER_SCHEMA-parameters"></a>

 *schema\_name*   
The name of the database schema to be altered\. 

RENAME TO   
A clause that renames the schema\. 

 *new\_name*   
The new name of the schema\. For more information about valid names, see [Names and identifiers](r_names.md)\. 

OWNER TO   
A clause that changes the owner of the schema\. 

 *new\_owner*   
The new owner of the schema\. 

QUOTA   
The maximum amount of disk space that the specified schema can use\. This space is the collective size of all tables under the specified schema\. Amazon Redshift converts the selected value to megabytes\. Gigabytes is the default unit of measurement when you don't specify a value\.   
For more information about configuring schema quotas, see [CREATE SCHEMA](r_CREATE_SCHEMA.md)\.

## Examples<a name="r_ALTER_SCHEMA-examples"></a>

The following example renames the SALES schema to US\_SALES\.

```
alter schema sales
rename to us_sales;
```

The following example gives ownership of the US\_SALES schema to the user DWUSER\.

```
alter schema us_sales
owner to dwuser;
```

The following example changes the quota to 300 GB and removes the quota\.

```
alter schema us_sales QUOTA 300 GB;
alter schema us_sales QUOTA UNLIMITED;
```