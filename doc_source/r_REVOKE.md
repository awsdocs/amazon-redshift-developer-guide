# REVOKE<a name="r_REVOKE"></a>

Removes access privileges, such as privileges to create or update tables, from a user or user group\. 

You can't GRANT or REVOKE permissions on an external table\. Instead, grant or revoke the permissions on the external schema\.

Specify in the REVOKE statement the privileges that you want to remove\. To give privileges, use the [GRANT](r_GRANT.md) command\. 

## Syntax<a name="r_REVOKE-synopsis"></a>

```
REVOKE [ GRANT OPTION FOR ]
{ { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
ON { [ TABLE ] table_name [, ...] | ALL TABLES IN SCHEMA schema_name [, ...] }
FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
{ { CREATE | TEMPORARY | TEMP } [,...] | ALL [ PRIVILEGES ] }
ON DATABASE db_name [, ...]
FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
{ { CREATE | USAGE } [,...] | ALL [ PRIVILEGES ] }
ON SCHEMA schema_name [, ...]
FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
EXECUTE 
    ON FUNCTION function_name ( [ [ argname ] argtype [, ...] ] ) [, ...]
    FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
USAGE 
    ON LANGUAGE language_name [, ...]
    FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]
```

## Parameters<a name="r_REVOKE-parameters"></a>

GRANT OPTION FOR   
Revokes only the option to grant a specified privilege to other users and does not revoke the privilege itself\. GRANT OPTION can not be revoked from a group or from PUBLIC\.

SELECT   
Revokes the privilege to select data from a table or a view using a SELECT statement\.

INSERT   
Revokes the privilege to load data into a table using an INSERT statement or a COPY statement\. 

UPDATE   
Revokes the privilege to update a table column using an UPDATE statement\. 

DELETE   
Revokes the privilege to delete a data row from a table\.

REFERENCES   
Revokes the privilege to create a foreign key constraint\. You should revoke this privilege on both the referenced table and the referencing table\.

ALL \[ PRIVILEGES \]   
Revokes all available privileges at once from the specified user or group\. The PRIVILEGES keyword is optional\.

ON \[ TABLE \] *table\_name*   
Revokes the specified privileges on a table or a view\. The TABLE keyword is optional\.

ON ALL TABLES IN SCHEMA *schema\_name*   
Revokes the specified privileges on all tables in the referenced schema\.

GROUP *group\_name*   
Revokes the privileges from the specified user group\.

PUBLIC   
Revokes the specified privileges from all users\. PUBLIC represents a group that always includes all users\. An individual user's privileges consist of the sum of privileges granted to PUBLIC, privileges granted to any groups that the user belongs to, and any privileges granted to the user individually\.

CREATE   
Depending on the database object, revokes the following privileges from the user or group:  

+ For databases, using the CREATE clause for REVOKE prevents users from creating schemas within the database\.

+ For schemas, using the CREATE clause for REVOKE prevents users from creating objects within a schema\. To rename an object, the user must have the CREATE privilege and own the object to be renamed\. 
By default, all users have CREATE and USAGE privileges on the PUBLIC schema\.

TEMPORARY | TEMP   
Revokes the privilege to create temporary tables in the specified database\.  
By default, users are granted permission to create temporary tables by their automatic membership in the PUBLIC group\. To remove the privilege for any users to create temporary tables, revoke the TEMP permission from the PUBLIC group and then explicitly grant the permission to create temporary tables to specific users or groups of users\.

ON DATABASE *db\_name*   
Revokes the privileges on the specified database\.

USAGE   
Revokes USAGE privileges on objects within a specific schema, which makes these objects inaccessible to users\. Specific actions on these objects must be revoked separately \(such as the EXECUTE privilege on functions\)\.  
By default, all users have CREATE and USAGE privileges on the PUBLIC schema\.

ON SCHEMA *schema\_name*   
Revokes the privileges on the specified schema\. You can use schema privileges to control the creation of tables; the CREATE privilege for a database only controls the creation of schemas\.

CASCADE   
If a user holds a privilege with grant option and has granted the privilege to other users, the privileges held by those other users are *dependent privileges*\. If the privilege or the grant option held by the first user is being revoked and dependent privileges exist, those dependent privileges are also revoked if CASCADE is specified; otherwise, the revoke action fails\.  
For example, if user A has granted a privilege with grant option to user B, and user B has granted the privilege to user C, user A can revoke the grant option from user B and use the CASCADE option to in turn revoke the privilege from user C\.

RESTRICT   
Revokes only those privileges that the user directly granted\. This behavior is the default\.

EXECUTE ON FUNCTION *function\_name*   
Revokes the EXECUTE privilege on a specific function\. Because function names can be overloaded, you must include the argument list for the function\. For more information, see [Naming UDFs](udf-naming-udfs.md)\.

USAGE ON LANGUAGE *language\_name*   
Revokes the USAGE privilege on a language\. For Python UDFs, use `plpythonu`\. For SQL UDFs, use `sql`\.  
To create a UDF, you must have permission for usage on language for SQL or plpythonu \(Python\)\. By default, USAGE ON LANGUAGE SQL is granted to PUBLIC, but you must explicitly grant USAGE ON LANGUAGE PLPYTHONU to specific users or groups\.   
To revoke usage for SQL, first revoke usage from PUBLIC, then grant usage on SQL only to the specific users or groups permitted to create SQL UDFs\. The following example revokes usage on SQL from PUBLIC then grants usage to the user group `udf_devs`\.   

```
revoke usage on language sql from PUBLIC;
grant usage on language sql to group udf_devs;
```
For more information, see [UDF Security and Privileges](udf-security-and-privileges.md)\. 

## Usage Notes<a name="r_REVOKE-usage-notes"></a>

To revoke privileges from an object, you must meet one of the following criteria:

+ Be the object owner\.

+ Be a superuser\.

+ Have a grant privilege for that object and privilege\.

  For example, the following command gives the user HR the ability both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users: 

  ```
  grant select on table employees to HR with grant option;
  ```

  Note that HR can't revoke privileges for any operation other than SELECT, or on any other table than employees\. 

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

PUBLIC represents a group that always includes all users\. By default all members of PUBLIC have CREATE and USAGE privileges on the PUBLIC schema\. To restrict any user's permissions on the PUBLIC schema, you must first revoke all permissions from PUBLIC on the PUBLIC schema, then grant privileges to specific users or groups\. The following example controls table creation privileges in the PUBLIC schema\.

```
revoke create on schema public from public;
```

## Examples<a name="r_REVOKE-examples"></a>

The following example revokes INSERT privileges on the SALES table from the GUESTS user group\. This command prevents members of GUESTS from being able to load data into the SALES table by using the INSERT command: 

```
revoke insert on table sales from group guests;
```

The following example revokes the SELECT privilege on all tables in the QA\_TICKIT schema from the user `fred`: 

```
revoke select on all tables in schema qa_tickit from fred;
```

The following example revokes the privilege to select from a view for user `bobr`: 

```
revoke select on table eventview from bobr;
```

The following example revokes the privilege to create temporary tables in the TICKIT database from all users: 

```
revoke temporary on database tickit from public;
```