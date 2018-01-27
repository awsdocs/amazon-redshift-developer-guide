# GRANT<a name="r_GRANT"></a>

Defines access privileges for a user or user group\.

Privileges include access options such as being able to read data in tables and views, write data, and create tables\. Use this command to give specific privileges for a table, database, schema, or function\. To revoke privileges from a database object, use the [REVOKE](r_REVOKE.md) command\. 

You can't GRANT or REVOKE permissions on an external table\. Instead, grant or revoke the permissions on the external schema\.

## Syntax<a name="r_GRANT-synopsis"></a>

  

```
GRANT { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...] | ALL TABLES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | TEMPORARY | TEMP } [,...] | ALL [ PRIVILEGES ] ]
    ON DATABASE db_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | USAGE } [,...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT EXECUTE 
    ON { [ FUNCTION ] function_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT USAGE 
    ON LANGUAGE language_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
```

## Parameters<a name="r_GRANT-parameters"></a>

SELECT   
Grants privilege to select data from a table or view using a SELECT statement\. The SELECT privilege is also required to reference existing column values for UPDATE or DELETE operations\.

INSERT   
Grants privilege to load data into a table using an INSERT statement or a COPY statement\. 

UPDATE   
Grants privilege to update a table column using an UPDATE statement\. UPDATE operations also require the SELECT privilege, because they must reference table columns to determine which rows to update, or to compute new values for columns\.

DELETE   
Grants privilege to delete a data row from a table\. DELETE operations also require the SELECT privilege, because they must reference table columns to determine which rows to delete\.

REFERENCES   
Grants privilege to create a foreign key constraint\. You need to grant this privilege on both the referenced table and the referencing table; otherwise, the user cannot create the constraint\. 

ALL \[ PRIVILEGES \]   
Grants all available privileges at once to the specified user or user group\. The PRIVILEGES keyword is optional\.  
GRANT ALL ON SCHEMA does not grant CREATE privileges for external schemas\.

ON \[ TABLE \] *table\_name*   
Grants the specified privileges on a table or a view\. The TABLE keyword is optional\. You can list multiple tables and views in one statement\.

ON ALL TABLES IN SCHEMA *schema\_name*   
Grants the specified privileges on all tables and views in the referenced schema\.

TO *username*   
Indicates the user receiving the privileges\.

WITH GRANT OPTION   
Indicates that the user receiving the privileges can in turn grant the same privileges to others\. WITH GRANT OPTION can not be granted to a group or to PUBLIC\.

GROUP *group\_name*   
Grants the privileges to a user group\.

PUBLIC   
Grants the specified privileges to all users, including users created later\. PUBLIC represents a group that always includes all users\. An individual user's privileges consist of the sum of privileges granted to PUBLIC, privileges granted to any groups that the user belongs to, and any privileges granted to the user individually\.

CREATE   
Depending on the database object, grants the following privileges to the user or user group:  

+ For databases, CREATE allows users to create schemas within the database\.

+ For schemas, CREATE allows users to create objects within a schema\. To rename an object, the user must have the CREATE privilege and own the object to be renamed\.

+ CREATE ON SCHEMA isn't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

TEMPORARY | TEMP   
Grants the privilege to create temporary tables in the specified database\. To run Amazon Redshift Spectrum queries, the database user must have permission to create temporary tables in the database\.   
By default, users are granted permission to create temporary tables by their automatic membership in the PUBLIC group\. To remove the privilege for any users to create temporary tables, revoke the TEMP permission from the PUBLIC group and then explicitly grant the permission to create temporary tables to specific users or groups of users\.

ON DATABASE *db\_name*   
Grants the specified privileges on a database\.

USAGE   
Grants USAGE privilege on a specific schema, which makes objects in that schema accessible to users\. Specific actions on these objects must be granted separately \(for example, SELECT or UPDATE privileges on tables\)\. By default, all users have CREATE and USAGE privileges on the PUBLIC schema\. 

ON SCHEMA *schema\_name*   
Grants the specified privileges on a schema\.  
GRANT CREATE ON SCHEMA and the CREATE privilege in GRANT ALL ON SCHEMA aren't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

EXECUTE ON \[ FUNCTION \] *function\_name*   
Grants the EXECUTE privilege on a specific function\. Because function names can be overloaded, you must include the argument list for the function\. For more information, see [Naming UDFs](udf-naming-udfs.md)\.

EXECUTE ON ALL FUNCTIONS IN SCHEMA *schema\_name*  
Grants the specified privileges on all functions in the referenced schema\.

USAGE ON LANGUAGE *language\_name*   
Grants the USAGE privilege on a language\. The USAGE ON LANGUAGE privilege is required to create UDFs by executing the [CREATE FUNCTION](r_CREATE_FUNCTION.md) command\. For more information, see [UDF Security and Privileges](udf-security-and-privileges.md)\.  
For Python UDFs, use `plpythonu` \. For SQL UDFs, use `sql` \.

## Usage Notes<a name="r_GRANT-usage-notes"></a>

To grant privileges on an object, you must meet one of the following criteria:

+ Be the object owner\.

+ Be a superuser\.

+ Have a grant privilege for that object and privilege\.

For example, the following command gives the user HR the ability both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users: 

```
grant select on table employees to HR with grant option;
```

Note that HR cannot grant privileges for any operation other than SELECT, or on any other table than employees\. 

Having privileges granted on a view does not imply having privileges on the underlying tables\. Similarly, having privileges granted on a schema does not imply having privileges on the tables in the schema\. You need to grant access to the underlying tables explicitly\.

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

## Examples<a name="r_GRANT-examples"></a>

 The following example grants the SELECT privilege on the SALES table to the user `fred`\. 

```
grant select on table sales to fred;
```

The following example grants the SELECT privilege on all tables in the QA\_TICKIT schema to the user `fred`\. 

```
grant select on all tables in schema qa_tickit to fred;
```

The following example grants all schema privileges on the schema QA\_TICKIT to the user group QA\_USERS\. Schema privileges are CREATE and USAGE\. USAGE grants users access to the objects in the schema, but does not grant privileges such as INSERT or SELECT on those objects\. Privileges must be granted on each object separately\.

```
create group qa_users;
grant all on schema qa_tickit to group qa_users;
```

The following example grants all privileges on the SALES table in the QA\_TICKIT schema to all users in the group QA\_USERS\. 

```
grant all on table qa_tickit.sales to group qa_users;
```

The following sequence of commands shows how access to a schema does not grant privileges on a table in the schema\. 

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

The following sequence of commands shows how access to a view does not imply access to its underlying tables\. The user called VIEW\_USER cannot select from the DATE table, although this user has been granted all privileges on VIEW\_DATE\. 

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