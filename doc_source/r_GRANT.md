# GRANT<a name="r_GRANT"></a>

Defines access privileges for a user or user group\.

Privileges include access options such as being able to read data in tables and views, write data, create tables, and drop tables\. Use this command to give specific privileges for a table, database, schema, function, procedure, language, or column\. To revoke privileges from a database object, use the [REVOKE](r_REVOKE.md) command\. 

Privileges also include access options such as being able to add objects or consumers to or remove objects or consumers from a datashare\. To add database objects to or remove database objects from a datashare for a user or user group, use the ALTER privilege\.  Similarly, to add or remove consumers from a datashare, use the SHARE privilege\. To revoke privileges from a database object, use the [REVOKE](r_REVOKE.md) command\. ALTER and SHARE are the only privileges that you can grant to users and user groups\. 

You can only GRANT or REVOKE USAGE permissions on an external schema to database users and user groups that use the ON SCHEMA syntax\. When using ON EXTERNAL SCHEMA with AWS Lake Formation, you can only GRANT and REVOKE privileges to an AWS Identity and Access Management \(IAM\) role\. For the list of privileges, see the syntax\.

For stored procedures, the only privilege that you can grant is EXECUTE\.

You can't run GRANT \(on an external resource\) within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_GRANT-synopsis"></a>



```
GRANT { { SELECT | INSERT | UPDATE | DELETE | DROP | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
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

The following is the syntax for the ASSUMEROLE privilege granted to users and groups with a specified role\. To begin using the ASSUMEROLE privilege, see [Usage notes for granting the ASSUMEROLE privilege ](r_GRANT-usage-notes.md#r_GRANT-usage-notes-assumerole)\.

```
GRANT ASSUMEROLE
    ON { 'iam_role' [, ...] | ALL }
    TO { username | GROUP group_name | PUBLIC } [, ...]
    FOR { ALL | COPY | UNLOAD | EXTERNAL FUNCTION | CREATE MODEL } [, ...]
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

The following is the syntax for using GRANT for datashare privileges on Amazon Redshift\. ALTER and SHARE are the only privileges that you can grant to users and user groups in this case\.

```
GRANT { ALTER | SHARE } ON DATASHARE datashare_name     
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for using GRANT for datashare usage privileges on Amazon Redshift\. You grant access to a datashare to a consumer using the USAGE privilege\. You can't grant this privilege to users or user groups\. This privilege also doesn't support the WITH GRANT OPTION for the GRANT statement\. Only users or user groups with the SHARE privilege previously granted to them FOR the datashare can run this type of GRANT statement\.

```
GRANT USAGE 
    ON DATASHARE datashare_name 
    TO NAMESPACE 'namespaceGUID' [, ...] | ACCOUNT 'accountnumber' [, ...]
```

The following is the syntax for GRANT data\-sharing usage permissions on a specific database or schema created from a datashare\. This USAGE permission doesn't grant usage permission to databases that aren't created from the specified datashare\. You can only GRANT or REVOKE ALTER or SHARE permissions on a datashare to users and user groups\.

```
GRANT USAGE ON { DATABASE shared_database_name [, ...] | SCHEMA shared_schema}
    TO { username | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for machine learning model privileges on Amazon Redshift\.

```
GRANT CREATE MODEL
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON MODEL model_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
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
Grants privilege to drop a table\. This privilege applies in Amazon Redshift and in an AWS Glue Data Catalog that is enabled for Lake Formation\.

ASSUMEROLE  <a name="assumerole"></a>
Grants privilege to run COPY, UNLOAD, EXTERNAL FUNCTION, and CREATE MODEL commands to users and groups with a specified role\. The user or group assumes that role when running the specified command\. To begin using the ASSUMEROLE privilege, see [Usage notes for granting the ASSUMEROLE privilege ](r_GRANT-usage-notes.md#r_GRANT-usage-notes-assumerole)\.

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

ON *iam\_role*   <a name="grant-iam_role"></a>
Grants the specified privileges to an IAM role\.

TO *username*   <a name="grant-to"></a>
Indicates the user receiving the privileges\.

TO IAM\_ROLE *iam\_role*   <a name="grant-to-iam-role"></a>
Indicates the IAM role receiving the privileges\.

WITH GRANT OPTION   <a name="grant-with-grant"></a>
Indicates that the user receiving the privileges can in turn grant the same privileges to others\. WITH GRANT OPTION can't be granted to a group or to PUBLIC\.

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
 

FOR \{ ALL \| COPY \| UNLOAD \| EXTERNAL FUNCTION \| CREATE MODEL \} \[, \.\.\.\]   <a name="grant-for"></a>
Specifies the SQL command for which the privilege is granted\. You can specify ALL to grant the privilege on the COPY, UNLOAD, EXTERNAL FUNCTION, and CREATE MODEL statements\. This clause applies only to granting the ASSUMEROLE privilege\.

ALTER  
Grants the ALTER privilege to users to add or remove objects from a datashare, or to set the property PUBLICACCESSIBLE\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

SHARE  
Grants privileges to users and user groups to add data consumers to a datashare\. This privilege is required to enable the particular consumer \(account or namespace\) to access the datashare from their clusters\. The consumer can be the same or a different AWS account, with the same or a different cluster namespace as specified by a globally unique identifier \(GUID\)\.

ON DATASHARE *datashare\_name*   <a name="grant-datashare"></a>
Grants the specified privileges on the referenced datashare\.

USAGE  
When USAGE is granted to a consumer account or namespace within the same account, the specific consumer account or namespace within the account can access the datashare and the objects of the datashare in read\-only fashion\.  

TO NAMESPACE 'clusternamespace GUID'  
Indicates a namespace in the same account where consumers can receive the specified privileges to the datashare\. Namespaces use a 128\-bit alphanumeric GUID\.

TO ACCOUNT 'accountnumber'  
Indicates the number of another account whose consumers can receive the specified privileges to the datashare\.

ON DATABASE *shared\_database\_name> \[, \.\.\.\]*   <a name="grant-datashare"></a>
Grants the specified usage privileges on the specified database that is created in the specified datashare\.

ON SCHEMA* shared\_schema*   <a name="grant-datashare"></a>
Grants the specified privileges on the specified schema that is created in the specified datashare\.

CREATE MODEL  
Grants the CREATE MODEL privilege to specific users or user groups\.

ON MODEL *model\_name*  
Grants the EXECUTE privilege on a specific model\. 