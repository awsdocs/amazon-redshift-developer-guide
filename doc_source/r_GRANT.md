# GRANT<a name="r_GRANT"></a>

Defines access privileges for a user or user group\.

Privileges include access options such as being able to read data in tables and views, write data, and create tables\. Use this command to give specific privileges for a table, database, schema, function, procedure, language, or column\. To revoke privileges from a database object, use the [REVOKE](r_REVOKE.md) command\. 

You can only GRANT or REVOKE USAGE permissions on an external schema to database users and user groups that use the ON SCHEMA syntax\. When using ON EXTERNAL SCHEMA with AWS Lake Formation, you can only GRANT and REVOKE privileges to an AWS Identity and Access Management \(IAM\) role\. For the list of privileges, see the syntax\.

For stored procedures, the only privilege that you can grant is EXECUTE\.

You can't run GRANT \(on an external resource\) within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_GRANT-synopsis"></a>

```
GRANT { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...] | ALL TABLES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | TEMPORARY | TEMP } [,...] | ALL [ PRIVILEGES ] }
    ON DATABASE db_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | USAGE } [,...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { FUNCTION function_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { PROCEDURE procedure_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL PROCEDURES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT USAGE 
    ON LANGUAGE language_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for column\-level privileges on Amazon Redshift tables and views\. 

```
GRANT { { SELECT | UPDATE } ( column_name [, ...] ) [, ...] | ALL [ PRIVILEGES ] ( column_name [,...] ) }
     ON { [ TABLE ] table_name [, ...] }
     TO { username | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for Redshift Spectrum integration with Lake Formation\. 

```
GRANT { SELECT | ALL [ PRIVILEGES ] } ( column_list )
    ON EXTERNAL TABLE schema_name.table_name 
    TO { IAM_ROLE iam_role } [, ...] [ WITH GRANT OPTION ] 

GRANT { { SELECT | ALTER | DROP | DELETE | INSERT }  [, ...] | ALL [ PRIVILEGES ] }
    ON EXTERNAL TABLE schema_name.table_name [, ...] 
    TO { { IAM_ROLE iam_role } [, ...] | PUBLIC } [ WITH GRANT OPTION ]
        
GRANT { { CREATE | ALTER | DROP }  [, ...] | ALL [ PRIVILEGES ] }
    ON EXTERNAL SCHEMA schema_name [, ...] 
    TO { IAM_ROLE iam_role } [, ...] [ WITH GRANT OPTION ]
```

## Parameters<a name="r_GRANT-parameters"></a>

SELECT   <a name="grant-select"></a>
Grants privilege to select data from a table or view using a SELECT statement\. The SELECT privilege is also required to reference existing column values for UPDATE or DELETE operations\.

INSERT   <a name="grant-insert"></a>
Grants privilege to load data into a table using an INSERT statement or a COPY statement\. 

UPDATE   <a name="grant-update"></a>
Grants privilege to update a table column using an UPDATE statement\. UPDATE operations also require the SELECT privilege, because they must reference table columns to determine which rows to update, or to compute new values for columns\.

DELETE  <a name="grant-delete"></a>
Grants privilege to delete a data row from a table\. DELETE operations also require the SELECT privilege, because they must reference table columns to determine which rows to delete\.

REFERENCES   <a name="grant-references"></a>
Grants privilege to create a foreign key constraint\. You need to grant this privilege on both the referenced table and the referencing table; otherwise, the user can't create the constraint\. 

ALL \[ PRIVILEGES \]   <a name="grant-all"></a>
Grants all available privileges at once to the specified user or user group\. The PRIVILEGES keyword is optional\.  
GRANT ALL ON SCHEMA doesn't grant CREATE privileges for external schemas\.  
You can grant ALL privilege to a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. In this case, individual privileges \(such as SELECT, ALTER, and so on\) are recorded in the Data Catalog\. 

ALTER  <a name="grant-alter"></a>
Grants privilege to alter a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. This privilege only applies when using Lake Formation\. 

DROP  <a name="grant-drop"></a>
Grants privilege to drop a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. This privilege only applies when using Lake Formation\. 

ON \[ TABLE \] *table\_name*   <a name="grant-on-table"></a>
Grants the specified privileges on a table or a view\. The TABLE keyword is optional\. You can list multiple tables and views in one statement\.

ON ALL TABLES IN SCHEMA *schema\_name*   <a name="grant-all-tables"></a>
Grants the specified privileges on all tables and views in the referenced schema\.

\( *column\_name* \[,\.\.\.\] \) ON TABLE *table\_name*   <a name="grant-column-level-privileges"></a>
Grants the specified privileges to users, groups, or PUBLIC on the specified columns of the Amazon Redshift table or view\.

\( *column\_list* \) ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="grant-external-table-column"></a>
Grants the specified privileges to an IAM role on the specified columns of the Lake Formation table in the referenced schema\.

ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="grant-external-table"></a>
Grants the specified privileges to an IAM role on the specified Lake Formation tables in the referenced schema\.

ON EXTERNAL SCHEMA *schema\_name*   <a name="grant-external-schema"></a>
Grants the specified privileges to an IAM role on the referenced schema\.

TO *username*   <a name="grant-to"></a>
Indicates the user receiving the privileges\.

TO IAM\_ROLE *iam\_role*   <a name="grant-to-iam-role"></a>
Indicates the IAM role receiving the privileges\.

WITH GRANT OPTION   <a name="grant-with-grant"></a>
Indicates that the user receiving the privileges can in turn grant the same privileges to others\. WITH GRANT OPTION can not be granted to a group or to PUBLIC\.

GROUP *group\_name*   <a name="grant-group"></a>
Grants the privileges to a user group\.

PUBLIC   <a name="grant-public"></a>
Grants the specified privileges to all users, including users created later\. PUBLIC represents a group that always includes all users\. An individual user's privileges consist of the sum of privileges granted to PUBLIC, privileges granted to any groups that the user belongs to, and any privileges granted to the user individually\.  
Granting PUBLIC to a Lake Formation EXTERNAL TABLE results in granting the privilege to the Lake Formation *everyone* group\.

CREATE   <a name="grant-create"></a>
Depending on the database object, grants the following privileges to the user or user group:  
+ For databases, CREATE allows users to create schemas within the database\.
+ For schemas, CREATE allows users to create objects within a schema\. To rename an object, the user must have the CREATE privilege and own the object to be renamed\.
+ CREATE ON SCHEMA isn't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

TEMPORARY \| TEMP   <a name="grant-temporary"></a>
Grants the privilege to create temporary tables in the specified database\. To run Amazon Redshift Spectrum queries, the database user must have permission to create temporary tables in the database\.   
By default, users are granted permission to create temporary tables by their automatic membership in the PUBLIC group\. To remove the privilege for any users to create temporary tables, revoke the TEMP permission from the PUBLIC group\. Then explicitly grant the permission to create temporary tables to specific users or groups of users\.

ON DATABASE *db\_name*   <a name="grant-database"></a>
Grants the specified privileges on a database\.

USAGE   <a name="grant-usage"></a>
Grants USAGE privilege on a specific schema, which makes objects in that schema accessible to users\. Specific actions on these objects must be granted separately \(for example, SELECT or UPDATE privileges on tables\)\. By default, all users have CREATE and USAGE privileges on the PUBLIC schema\. 

ON SCHEMA *schema\_name*   <a name="grant-schema"></a>
Grants the specified privileges on a schema\.  
GRANT CREATE ON SCHEMA and the CREATE privilege in GRANT ALL ON SCHEMA aren't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

EXECUTE ON FUNCTION *function\_name*   <a name="grant-function"></a>
Grants the EXECUTE privilege on a specific function\. Because function names can be overloaded, you must include the argument list for the function\. For more information, see [Naming UDFs](udf-naming-udfs.md)\.

EXECUTE ON ALL FUNCTIONS IN SCHEMA *schema\_name*  <a name="grant-all-functions"></a>
Grants the specified privileges on all functions in the referenced schema\.

EXECUTE ON PROCEDURE *procedure\_name*   <a name="grant-procedure"></a>
Grants the EXECUTE privilege on a specific stored procedure\. Because stored procedure names can be overloaded, you must include the argument list for the procedure\. For more information, see [Naming stored procedures](stored-procedure-naming.md)\.

EXECUTE ON ALL PROCEDURES IN SCHEMA *schema\_name*  <a name="grant-all-procedures"></a>
Grants the specified privileges on all stored procedures in the referenced schema\.

USAGE ON LANGUAGE *language\_name*   
Grants the USAGE privilege on a language\.   
The USAGE ON LANGUAGE privilege is required to create user\-defined functions \(UDFs\) by running the [CREATE FUNCTION](r_CREATE_FUNCTION.md) command\. For more information, see [UDF security and privileges](udf-security-and-privileges.md)\.   
The USAGE ON LANGUAGE privilege is required to create stored procedures by running the [CREATE PROCEDURE](r_CREATE_PROCEDURE.md) command\. For more information, see [Security and privileges for stored procedures ](stored-procedure-security-and-privileges.md)\.  
For Python UDFs, use `plpythonu`\. For SQL UDFs, use `sql`\. For stored procedures, use `plpgsql`\.

## Usage notes<a name="r_GRANT-usage-notes"></a>

To grant privileges on an object, you must meet one of the following criteria:
+ Be the object owner\.
+ Be a superuser\.
+ Have a grant privilege for that object and privilege\.

For example, the following command enables the user HR both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users\.

```
grant select on table employees to HR with grant option;
```

HR can't grant privileges for any operation other than SELECT, or on any other table than employees\. 

Having privileges granted on a view doesn't imply having privileges on the underlying tables\. Similarly, having privileges granted on a schema doesn't imply having privileges on the tables in the schema\. Instead, grant access to the underlying tables explicitly\.

To grant privileges to an AWS Lake Formation table, the IAM role associated with the table's external schema must have permission to grant privileges to the external table\. The following example creates an external schema with an associated IAM role `myGrantor`\. The IAM role `myGrantor` has the permission to grant permissions to others\. The GRANT command uses the permission of the IAM role `myGrantor` that is associated with the external schema to grant permission to the IAM role `myGrantee`\.

```
create external schema mySchema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/myGrantor'
create external database if not exists;
```

```
grant select 
on external table mySchema.mytable
to iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

If you GRANT ALL privileges to an IAM role, individual privileges are granted in the related Lake Formation–enabled Data Catalog\. For example, the following GRANT ALL results in the granted individual privileges \(SELECT, ALTER, DROP, DELETE, and INSERT\) showing in the Lake Formation console\.

```
grant all 
on external table mySchema.mytable
to iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

## Usage notes for column\-level access control<a name="r_GRANT-usage-notes-clp"></a>

The following usage notes apply to column\-level privileges on Amazon Redshift tables and views\. These notes describe tables; the same notes apply to views unless we explicitly note an exception\. 

For an Amazon Redshift table, you can grant only the SELECT and UPDATE privileges at the column level\. For an Amazon Redshift view, you can grant only the SELECT privilege at the column level\. 

The ALL keyword is a synonym for SELECT and UPDATE privileges combined when used in the context of a column\-level GRANT on a table\. 

If you don't have SELECT privilege on all columns in a table, performing a SELECT operation for all columns \(`SELECT *`\) fails\.

If you have SELECT or UPDATE privilege on a table or view and add a column, you still have the same privileges on the table or view and thus all its columns\. 

Only a table's owner or a superuser can grant column\-level privileges\. 

The WITH GRANT OPTION clause isn't supported for column\-level privileges\.

You can't hold the same privilege at both the table level and the column level\. For example, the user `data_scientist` can't have both SELECT privilege on the table `employee` and SELECT privilege on the column `employee.department`\. Consider the following results when granting the same privilege to a table and a column within the table:
+ If a user has a table\-level privilege on a table, then granting the same privilege at the column level has no effect\. 
+ If a user has a table\-level privilege on a table, then revoking the same privilege for one or more columns of the table returns an error\. Instead, revoke the privilege at the table level\. 
+ If a user has a column\-level privilege, then granting the same privilege at the table level returns an error\. 
+ If a user has a column\-level privilege, then revoking the same privilege at the table level revokes both column and table privileges for all columns on the table\. 

You can't grant column\-level privileges on late\-binding views\.

You must have table\-level SELECT privilege on the base tables to create a materialized view\. Even if you have column\-level privileges on specific columns, you can't create a materialized view on only those columns\. However, you can grant SELECT privilege to columns of a materialized view, similar to regular views\. 

To look up grants of column\-level privileges, use the [PG\_ATTRIBUTE\_INFO](r_PG_ATTRIBUTE_INFO.md) view\. 

## Examples<a name="r_GRANT-examples"></a>

 The following example grants the SELECT privilege on the SALES table to the user `fred`\. 

```
grant select on table sales to fred;
```

The following example grants the SELECT privilege on all tables in the QA\_TICKIT schema to the user `fred`\. 

```
grant select on all tables in schema qa_tickit to fred;
```

The following example grants all schema privileges on the schema QA\_TICKIT to the user group QA\_USERS\. Schema privileges are CREATE and USAGE\. USAGE grants users access to the objects in the schema, but doesn't grant privileges such as INSERT or SELECT on those objects\. Grant privileges on each object separately\.

```
create group qa_users;
grant all on schema qa_tickit to group qa_users;
```

The following example grants all privileges on the SALES table in the QA\_TICKIT schema to all users in the group QA\_USERS\. 

```
grant all on table qa_tickit.sales to group qa_users;
```

The following sequence of commands shows how access to a schema doesn't grant privileges on a table in the schema\. 

```
create user schema_user in group qa_users password 'Abcd1234';
create schema qa_tickit;
create table qa_tickit.test (col1 int);
grant all on schema qa_tickit to schema_user;

set session authorization schema_user;
select current_user;

current_user
--------------
schema_user
(1 row)

select count(*) from qa_tickit.test;

ERROR: permission denied for relation test [SQL State=42501] 

set session authorization dw_user;
grant select on table qa_tickit.test to schema_user;
set session authorization schema_user;
select count(*) from qa_tickit.test;

count
-------
0
(1 row)
```

The following sequence of commands shows how access to a view doesn't imply access to its underlying tables\. The user called VIEW\_USER can't select from the DATE table, although this user has been granted all privileges on VIEW\_DATE\. 

```
create user view_user password 'Abcd1234';
create view view_date as select * from date;
grant all on view_date to view_user;
set session authorization view_user;
select current_user;

current_user
--------------
view_user
(1 row)

select count(*) from view_date;
count
-------
365
(1 row)

select count(*) from date;
ERROR:  permission denied for relation date
```

The following example grants SELECT privilege on the `cust_name` and `cust_phone` columns of the `cust_profile` table to the user `user1`\. 

```
grant select(cust_name, cust_phone) on cust_profile to user1;
```

The following example grants SELECT privilege on the `cust_name` and `cust_phone` columns and UPDATE privilege on the `cust_contact_preference` column of the `cust_profile` table to the `sales_group` group\. 

```
grant select(cust_name, cust_phone), update(cust_contact_preference) on cust_profile to group sales_group;
```

The following example shows the usage of the ALL keyword to grant both SELECT and UPDATE privileges on three columns of the table `cust_profile` to the `sales_admin` group\. 

```
grant ALL(cust_name, cust_phone,cust_contact_preference) on cust_profile to group sales_admin;
```

The following example grants the SELECT privilege on the `cust_name` column of the `cust_profile_vw` view to the `user2` user\. 

```
grant select(cust_name) on cust_profile_vw to user2;
```