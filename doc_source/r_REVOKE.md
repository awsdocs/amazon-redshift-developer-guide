# REVOKE<a name="r_REVOKE"></a>

Removes access privileges, such as privileges to create or update tables, from a user or user group\. 

You can only GRANT or REVOKE USAGE permissions on an external schema to database users and user groups using the ON SCHEMA syntax\. When using ON EXTERNAL SCHEMA with AWS Lake Formation, you can only GRANT and REVOKE privileges to an AWS Identity and Access Management \(IAM\) role\. For the list of privileges, see the syntax\.

For stored procedures, USAGE ON LANGUAGE `plpgsql` permissions are granted to PUBLIC by default\. EXECUTE ON PROCEDURE permission is granted only to the owner and superusers by default\.

Specify in the REVOKE command the privileges that you want to remove\. To give privileges, use the [GRANT](r_GRANT.md) command\. 

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
{ { EXECUTE } [,...] | ALL [ PRIVILEGES ] }
    ON PROCEDURE procedure_name ( [ [ argname ] argtype [, ...] ] ) [, ...]
    FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
USAGE 
    ON LANGUAGE language_name [, ...]
    FROM { username | GROUP group_name | PUBLIC } [, ...]
[ CASCADE | RESTRICT ]
```

The following is the syntax for column\-level privileges on Amazon Redshift tables and views\. 

```
REVOKE { { SELECT | UPDATE } ( column_name [, ...] ) [, ...] | ALL [ PRIVILEGES ] ( column_name [,...] ) }
     ON { [ TABLE ] table_name [, ...] }
     FROM { username | GROUP group_name | PUBLIC } [, ...] 
     [ CASCADE | RESTRICT ]
```

The following is the syntax for Redshift Spectrum integration with Lake Formation\. 

```
REVOKE [ GRANT OPTION FOR ]
{ SELECT | ALL [ PRIVILEGES ] } ( column_list )
    ON EXTERNAL TABLE schema_name.table_name 
    FROM { IAM_ROLE iam_role } [, ...]  

REVOKE [ GRANT OPTION FOR ]
{ { SELECT | ALTER | DROP | DELETE | INSERT }  [, ...] | ALL [ PRIVILEGES ] }
    ON EXTERNAL TABLE schema_name.table_name [, ...] 
    FROM { { IAM_ROLE iam_role } [, ...] | PUBLIC }

REVOKE [ GRANT OPTION FOR ]
{ { CREATE | ALTER | DROP }  [, ...] | ALL [ PRIVILEGES ] }
    ON EXTERNAL SCHEMA schema_name [, ...] 
    FROM { IAM_ROLE iam_role } [, ...]
```

## Parameters<a name="r_REVOKE-parameters"></a>

GRANT OPTION FOR   
Revokes only the option to grant a specified privilege to other users and doesn't revoke the privilege itself\. You can't revoke GRANT OPTION from a group or from PUBLIC\.

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

ALTER  
Revokes privilege to alter a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. This privilege only applies when using Lake Formation\. 

DROP  
Revokes privilege to drop a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. This privilege only applies when using Lake Formation\. 

ON \[ TABLE \] *table\_name*   
Revokes the specified privileges on a table or a view\. The TABLE keyword is optional\.

ON ALL TABLES IN SCHEMA *schema\_name*   
Revokes the specified privileges on all tables in the referenced schema\.

\( *column\_name* \[,\.\.\.\] \) ON TABLE *table\_name*   <a name="revoke-column-level-privileges"></a>
Revokes the specified privileges from users, groups, or PUBLIC on the specified columns of the Amazon Redshift table or view\.

\( *column\_list* \) ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="revoke-external-table-column"></a>
Revokes the specified privileges from an IAM role on the specified columns of the Lake Formation table in the referenced schema\.

ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="revoke-external-table"></a>
Revokes the specified privileges from an IAM role on the specified Lake Formation tables in the referenced schema\.

ON EXTERNAL SCHEMA *schema\_name*   <a name="revoke-external-schema"></a>
Revokes the specified privileges from an IAM role on the referenced schema\.

FROM IAM\_ROLE *iam\_role*   <a name="revoke-from-iam-role"></a>
Indicates the IAM role losing the privileges\.

GROUP *group\_name*   
Revokes the privileges from the specified user group\.

PUBLIC   
Revokes the specified privileges from all users\. PUBLIC represents a group that always includes all users\. An individual user's privileges consist of the sum of privileges granted to PUBLIC, privileges granted to any groups that the user belongs to, and any privileges granted to the user individually\.  
Revoking PUBLIC from a Lake Formation external table results in revoking the privilege from the Lake Formation *everyone* group\.

CREATE   
Depending on the database object, revokes the following privileges from the user or group:  
+ For databases, using the CREATE clause for REVOKE prevents users from creating schemas within the database\.
+ For schemas, using the CREATE clause for REVOKE prevents users from creating objects within a schema\. To rename an object, the user must have the CREATE privilege and own the object to be renamed\. 
By default, all users have CREATE and USAGE privileges on the PUBLIC schema\.

TEMPORARY \| TEMP   
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

EXECUTE ON PROCEDURE *procedure\_name*   
Revokes the EXECUTE privilege on a specific stored procedure\. Because stored procedure names can be overloaded, you must include the argument list for the procedure\. For more information, see [Naming stored procedures](stored-procedure-naming.md)\.

EXECUTE ON ALL PROCEDURES IN SCHEMA *procedure\_name*   
Revokes the specified privileges on all procedures in the referenced schema\.

USAGE ON LANGUAGE *language\_name*   
Revokes the USAGE privilege on a language\. For Python user\-defined functions \(UDFs\), use `plpythonu`\. For SQL UDFs, use `sql`\. For stored procedures, use `plpgsql`\.   
To create a UDF, you must have permission for usage on language for SQL or `plpythonu` \(Python\)\. By default, USAGE ON LANGUAGE SQL is granted to PUBLIC\. However, you must explicitly grant USAGE ON LANGUAGE PLPYTHONU to specific users or groups\.   
To revoke usage for SQL, first revoke usage from PUBLIC\. Then grant usage on SQL only to the specific users or groups permitted to create SQL UDFs\. The following example revokes usage on SQL from PUBLIC then grants usage to the user group `udf_devs`\.   

```
revoke usage on language sql from PUBLIC;
grant usage on language sql to group udf_devs;
```
For more information, see [UDF security and privileges](udf-security-and-privileges.md)\.   
To revoke usage for stored procedures, first revoke usage from PUBLIC\. Then grant usage on `plpgsql` only to the specific users or groups permitted to create stored procedures\. For more information, see [Security and privileges for stored procedures ](stored-procedure-security-and-privileges.md)\. 

## Usage notes<a name="r_REVOKE-usage-notes"></a>

To revoke privileges from an object, you must meet one of the following criteria:
+ Be the object owner\.
+ Be a superuser\.
+ Have a grant privilege for that object and privilege\.

  For example, the following command enables the user HR both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users\.

  ```
  grant select on table employees to HR with grant option;
  ```

  HR can't revoke privileges for any operation other than SELECT, or on any other table than employees\. 

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

PUBLIC represents a group that always includes all users\. By default all members of PUBLIC have CREATE and USAGE privileges on the PUBLIC schema\. To restrict any user's permissions on the PUBLIC schema, you must first revoke all permissions from PUBLIC on the PUBLIC schema, then grant privileges to specific users or groups\. The following example controls table creation privileges in the PUBLIC schema\.

```
revoke create on schema public from public;
```

To revoke privileges from a Lake Formation table, the IAM role associated with the table's external schema must have permission to revoke privileges to the external table\. The following example creates an external schema with an associated IAM role `myGrantor`\. IAM role `myGrantor` has the permission to revoke permissions from others\. The REVOKE command uses the permission of the IAM role `myGrantor` that is associated with the external schema to revoke permission to the IAM role `myGrantee`\.

```
create external schema mySchema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/myGrantor'
create external database if not exists;
```

```
revoke select 
on external table mySchema.mytable
from iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

**Note**  
If the IAM role also has the `ALL` permission in an AWS Glue Data Catalog that is enabled for Lake Formation, the `ALL` permission isn't revoked\. Only the `SELECT` permission is revoked\. You can view the Lake Formation permissions in the Lake Formation console\.

## Examples<a name="r_REVOKE-examples"></a>

The following example revokes INSERT privileges on the SALES table from the GUESTS user group\. This command prevents members of GUESTS from being able to load data into the SALES table by using the INSERT command\. 

```
revoke insert on table sales from group guests;
```

The following example revokes the SELECT privilege on all tables in the QA\_TICKIT schema from the user `fred`\.

```
revoke select on all tables in schema qa_tickit from fred;
```

The following example revokes the privilege to select from a view for user `bobr`\.

```
revoke select on table eventview from bobr;
```

The following example revokes the privilege to create temporary tables in the TICKIT database from all users\.

```
revoke temporary on database tickit from public;
```

The following example revokes SELECT privilege on the `cust_name` and `cust_phone` columns of the `cust_profile` table from the user `user1`\. 

```
revoke select(cust_name, cust_phone) on cust_profile from user1;
```

The following example revokes SELECT privilege on the `cust_name` and `cust_phone` columns and UPDATE privilege on the `cust_contact_preference` column of the `cust_profile` table from the `sales_group` group\. 

```
revoke select(cust_name, cust_phone), update(cust_contact_preference) on cust_profile from group sales_group;
```

The following example shows the usage of the ALL keyword to revoke both SELECT and UPDATE privileges on three columns of the table `cust_profile` from the `sales_admin` group\. 

```
revoke ALL(cust_name, cust_phone,cust_contact_preference) on cust_profile from group sales_admin;
```

The following example revokes the SELECT privilege on the `cust_name` column of the `cust_profile_vw` view from the `user2` user\. 

```
revoke select(cust_name) on cust_profile_vw from user2;
```