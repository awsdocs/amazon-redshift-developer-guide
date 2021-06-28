# REVOKE<a name="r_REVOKE"></a>

Removes access privileges, such as privileges to create, drop, or update tables, from a user or user group\.

You can only GRANT or REVOKE USAGE permissions on an external schema to database users and user groups using the ON SCHEMA syntax\. When using ON EXTERNAL SCHEMA with AWS Lake Formation, you can only GRANT and REVOKE privileges to an AWS Identity and Access Management \(IAM\) role\. For the list of privileges, see the syntax\.

For stored procedures, USAGE ON LANGUAGE `plpgsql` permissions are granted to PUBLIC by default\. EXECUTE ON PROCEDURE permission is granted only to the owner and superusers by default\.

Specify in the REVOKE command the privileges that you want to remove\. To give privileges, use the [GRANT](r_GRANT.md) command\. 

## Syntax<a name="r_REVOKE-synopsis"></a>

```
REVOKE [ GRANT OPTION FOR ]
{ { SELECT | INSERT | UPDATE | DELETE | DROP | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
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

The following is the syntax to revoke the ASSUMEROLE privilege from users and groups with a specified role\. 

```
REVOKE ASSUMEROLE
    ON { 'iam_role' [, ...] | ALL }
    FROM { user_name | GROUP group_name | PUBLIC } [, ...]
    FOR { ALL | COPY | UNLOAD | EXTERNAL FUNCTION | CREATE MODEL }
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

The following is the syntax for using REVOKE for datashare privileges for Amazon Redshift\. 

```
REVOKE { ALTER | SHARE } ON DATASHARE datashare_name     
    FROM { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for using REVOKE for datashare usage privileges for Amazon Redshift\. 

```
REVOKE USAGE 
    ON DATASHARE datashare_name 
    FROM NAMESPACE 'namespaceGUID' [, ...] | ACCOUNT 'accountnumber' [, ...]
```

The following is the REVOKE syntax for data\-sharing usage permissions on a specific database or schema created from a datashare\. This USAGE permission doesn't revoke usage permission to databases that are not created from the specified datashare\. You can only REVOKE, ALTER, or SHARE permissions on a datashare to users and user groups\.

```
REVOKE USAGE ON { DATABASE shared_database_name [, ...] | SCHEMA shared_schema}
    FROM { username | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for machine learning model privileges on Amazon Redshift\.

```
REVOKE [ GRANT OPTION FOR ]
    CREATE MODEL FROM { username | GROUP group_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { EXECUTE | ALL [ PRIVILEGES ] }
    ON MODEL model_name [, ...]
    FROM { username | GROUP group_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]
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
Revokes privilege to drop a table\. This privilege applies in Amazon Redshift and in an AWS Glue Data Catalog that is enabled for Lake Formation\.

ASSUMEROLE  <a name="assumerole"></a>
Revokes the privilege to run COPY, UNLOAD, EXTERNAL FUNCTION, or CREATE MODEL commands from users and groups with a specified role\. 

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

FOR \{ ALL \| COPY \| UNLOAD \| EXTERNAL FUNCTION \| CREATE MODEL \} \[, \.\.\.\]  <a name="revoke-for"></a>
Specifes the SQL command for which the privilege is revoked\. You can specify ALL to revoke the privilege on the COPY, UNLOAD, EXTERNAL FUNCTION, and CREATE MODEL statements\. This clause applies only to revoking the ASSUMEROLE privilege\.

ALTER  
Revokes the ALTER privilege for users or user groups that allows those that don't own a datashare to alter the datashare\. This privilege is required to add or remove objects from a datashare, or to set the property PUBLICACCESSIBLE\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

SHARE  
Revokes privileges for users and user groups to add consumers to a datashare\. Revoing this privilege is required to stop the particular consumer  from accessing the datashare from its clusters\. 

ON DATASHARE *datashare\_name *  
Grants the specified privileges on the referenced datashare\.

FROM username  
Indicates the user losing the privileges\.

FROM GROUP *group\_name*  
Indicates the user group losing the privileges\.

WITH GRANT OPTION  
Indicates that the user losing the privileges can in turn revoke the same privileges for others\. You can't revoke WITH GRANT OPTION for a group or for PUBLIC\.  

USAGE  
When USAGE is revoked for a consumer account or namespace within the same account, the specified consumer account or namespace within an account can't access the datashare and the objects of the datashare in read\-only fashion\.   
Revoking the USAGE privilege revokes the access to a datashare from consumers\. 

FROM NAMESPACE 'clusternamespace GUID'   
Indicates the namespace in the same account that has consumers losing the privileges to the datashare\. Namespaces use a 128\-bit alphanumeric globally unique identifier \(GUID\)\.

FROM ACCOUNT 'accountnumber'   
Indicates the account number of another account that has the consumers losing the privileges to the datashare\.

ON DATABASE *shared\_database\_name> \[, \.\.\.\]*   <a name="revoke-datashare"></a>
Revokes the specified usage privileges on the specified database that was created in the specified datashare\.  

ON SCHEMA* shared\_schema*   <a name="revoke-datashare"></a>
Revokes the specified privileges on the specified schema that was created in the specified datashare\.

CREATE MODEL  
Revokes the CREATE MODEL privilege to create machine learning models in the specified database\.

ON MODEL *model\_name*  
Revokes the EXECUTE privilege for a specific model\. 

 CASCADE   
Specifies to automatically drop dependent objects when Amazon Redshift drops the model\. Dependent objects include objects such as views and other SQL user\-defined functions\.

 RESTRICT   
Specifies to not drop dependent objects when Amazon Redshift drops the model\. Dependent objects include objects such as views and other SQL user\-defined functions\. This action is the default\.