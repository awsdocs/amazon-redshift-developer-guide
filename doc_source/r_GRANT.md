# GRANT<a name="r_GRANT"></a>

Defines access permissions for a user or user group\.

Permissions include access options such as being able to read data in tables and views, write data, create tables, and drop tables\. Use this command to give specific permissions for a table, database, schema, function, procedure, language, or column\. To revoke permissions from a database object, use the [REVOKE](r_REVOKE.md) command\. 

Permissions also include access options such as being able to add objects or consumers to or remove objects or consumers from a datashare\. To add database objects to or remove database objects from a datashare for a user or user group, use the ALTER permission\.  Similarly, to add or remove consumers from a datashare, use the SHARE permission\. To revoke permissions from a database object, use the [REVOKE](r_REVOKE.md) command\. ALTER and SHARE are the only permissions that you can grant to users and user groups\. 

You can also grant roles to manage database permissions and control what users can do relative to your data\. By defining roles and assigning roles to users, you can limit the the actions those users can take, such as limiting users to only the CREATE TABLE and INSERT commands\. For more information about the CREATE ROLE command, see [CREATE ROLE](r_CREATE_ROLE.md)\. Amazon Redshift has some system\-defined roles that you can also use to grant specific permissions to your users\. For more information, see [Amazon Redshift system\-defined roles](r_roles-default.md)\.

You can only GRANT or REVOKE USAGE permissions on an external schema to database users and user groups that use the ON SCHEMA syntax\. When using ON EXTERNAL SCHEMA with AWS Lake Formation, you can only GRANT and REVOKE permissions to an AWS Identity and Access Management \(IAM\) role\. For the list of permissions, see the syntax\.

For stored procedures, the only permission that you can grant is EXECUTE\.

You can't run GRANT \(on an external resource\) within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_GRANT-synopsis"></a>



```
GRANT { { SELECT | INSERT | UPDATE | DELETE | DROP | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...] | ALL TABLES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | TEMPORARY | TEMP } [,...] | ALL [ PRIVILEGES ] }
    ON DATABASE db_name [, ...]
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | USAGE } [,...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { FUNCTION function_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { PROCEDURE procedure_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL PROCEDURES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT USAGE
    ON LANGUAGE language_name [, ...]
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
```

### Granting column\-level permissions for tables<a name="grant-column-level"></a>

The following is the syntax for column\-level permissions on Amazon Redshift tables and views\.

```
GRANT { { SELECT | UPDATE } ( column_name [, ...] ) [, ...] | ALL [ PRIVILEGES ] ( column_name [,...] ) }
     ON { [ TABLE ] table_name [, ...] }

     TO { username | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
```

### Granting ASSUMEROLE permissions<a name="grant-assumerole-permissions"></a>

The following is the syntax for the ASSUMEROLE permissions granted to users and groups with a specified role\. To begin using the ASSUMEROLE privilege, see [Usage notes for granting the ASSUMEROLE permission](r_GRANT-usage-notes.md#r_GRANT-usage-notes-assumerole)\.

```
GRANT ASSUMEROLE
       ON { 'iam_role' [, ...] | default | ALL }
       TO { username | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
       FOR { ALL | COPY | UNLOAD | EXTERNAL FUNCTION | CREATE MODEL } [, ...]
```

### Granting permissions for Redshift Spectrum integration with Lake Formation<a name="grant-spectrum-integration-with-lf-syntax"></a>

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

### Granting datashare permissions<a name="grant-datashare-syntax"></a>

The following is the syntax for using GRANT for datashare permissions on Amazon Redshift\. ALTER and SHARE are the only permissions that you can grant to users and user groups in this case\.

```
GRANT { ALTER | SHARE } ON DATASHARE datashare_name
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for using GRANT for datashare usage permissions on Amazon Redshift\. You grant access to a datashare to a consumer using the USAGE permission\. You can't grant this permission to users or user groups\. This permission also doesn't support the WITH GRANT OPTION for the GRANT statement\. Only users or user groups with the SHARE permission previously granted to them FOR the datashare can run this type of GRANT statement\.

```
GRANT USAGE
    ON DATASHARE datashare_name
    TO NAMESPACE 'namespaceGUID' [, ...] | ACCOUNT 'accountnumber' [ VIA DATA CATALOG ] [, ...]
```

The following is an example of how to grant usage of a datashare to a Lake Formation account\.

```
GRANT USAGE ON salesshare TO ACCOUNT '123456789012' VIA DATA CATALOG;
```

The following is the syntax for GRANT data\-sharing usage permissions on a specific database or schema created from a datashare\. This USAGE permission doesn't grant usage permission to databases that aren't created from the specified datashare\. You can only GRANT or REVOKE ALTER or SHARE permissions on a datashare to users and user groups\.

```
GRANT USAGE ON { DATABASE shared_database_name [, ...] | SCHEMA shared_schema}
    TO { username | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
```

### Granting machine learning permissions<a name="grant-model-syntax"></a>

The following is the syntax for machine learning model permissions on Amazon Redshift\.

```
GRANT CREATE MODEL
    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON MODEL model_name [, ...]

    TO { username [ WITH GRANT OPTION ] | ROLE role_name | GROUP group_name | PUBLIC } [, ...]
```

### Granting role permissions<a name="grant-roles"></a>

The following is the syntax for granting role permissions on Amazon Redshift\.

```
GRANT { ROLE role_name } [, ...] TO { { user_name [ WITH ADMIN OPTION ] } | ROLE role_name }[, ...]
```

The following is the syntax for granting system permissions to roles on Amazon Redshift\.

```
GRANT
  {
    { CREATE USER | DROP USER | ALTER USER |
    CREATE SCHEMA | DROP SCHEMA |
    ALTER DEFAULT PRIVILEGES |
    CREATE TABLE | DROP TABLE | ALTER TABLE |
    CREATE OR REPLACE FUNCTION | CREATE OR REPLACE EXTERNAL FUNCTION |
    DROP FUNCTION |
    CREATE OR REPLACE PROCEDURE | DROP PROCEDURE |
    CREATE OR REPLACE VIEW | DROP VIEW |
    CREATE MODEL | DROP MODEL |
    CREATE DATASHARE | ALTER DATASHARE | DROP DATASHARE |
    CREATE LIBRARY | DROP LIBRARY |
    CREATE ROLE | DROP ROLE
    TRUNCATE TABLE
    VACUUM | ANALYZE | CANCEL }[, ...]
  }
  | { ALL [ PRIVILEGES ] }
TO { ROLE role_name } [, ...]
```

### Granting explain permissions for row\-level security policy filters<a name="grant-row-level-security"></a>

The following is the syntax for granting permissions to explain the row\-level security policy filters of a query in the EXPLAIN plan\. You can revoke the privilege using the REVOKE statement\.

```
GRANT EXPLAIN RLS TO ROLE rolename 
```

The following is the syntax for granting permissions to bypass row\-level security policies for a query\. 

```
GRANT IGNORE RLS TO ROLE rolename 
```

The following is the syntax for granting permissions to the specified row\-level security policy\.

```
GRANT SELECT ON [ TABLE ] table_name [, ...]
TO RLS POLICY policy_name [, ...]
```

## Parameters<a name="r_GRANT-parameters"></a>

SELECT   <a name="grant-select"></a>
Grants permission to select data from a table or view using a SELECT statement\. The SELECT permission is also required to reference existing column values for UPDATE or DELETE operations\.

INSERT   <a name="grant-insert"></a>
Grants permission to load data into a table using an INSERT statement or a COPY statement\. 

UPDATE   <a name="grant-update"></a>
Grants permission to update a table column using an UPDATE statement\. UPDATE operations also require the SELECT permission, because they must reference table columns to determine which rows to update, or to compute new values for columns\.

DELETE  <a name="grant-delete"></a>
Grants permission to delete a data row from a table\. DELETE operations also require the SELECT permission, because they must reference table columns to determine which rows to delete\.

REFERENCES   <a name="grant-references"></a>
Grants permission to create a foreign key constraint\. You need to grant this permission on both the referenced table and the referencing table; otherwise, the user can't create the constraint\. 

ALL \[ PRIVILEGES \]   <a name="grant-all"></a>
Grants all available permissions at once to the specified user or user group\. The PRIVILEGES keyword is optional\.  
GRANT ALL ON SCHEMA doesn't grant CREATE permissions for external schemas\.  
You can grant the ALL permission to a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. In this case, individual permissions \(such as SELECT, ALTER, and so on\) are recorded in the Data Catalog\. 

ALTER  <a name="grant-alter"></a>
Grants permission to alter a table in an AWS Glue Data Catalog that is enabled for Lake Formation\. This permission only applies when using Lake Formation\. 

DROP  <a name="grant-drop"></a>
Grants permission to drop a table\. This permission applies in Amazon Redshift and in an AWS Glue Data Catalog that is enabled for Lake Formation\.

ASSUMEROLE  <a name="assumerole"></a>
Grants permission to run COPY, UNLOAD, EXTERNAL FUNCTION, and CREATE MODEL commands to users, roles, or groups with a specified role\. The user, role, or group assumes that role when running the specified command\. To begin using the ASSUMEROLE permission, see [Usage notes for granting the ASSUMEROLE permission](r_GRANT-usage-notes.md#r_GRANT-usage-notes-assumerole)\.

ON \[ TABLE \] *table\_name*   <a name="grant-on-table"></a>
Grants the specified permissions on a table or a view\. The TABLE keyword is optional\. You can list multiple tables and views in one statement\.

ON ALL TABLES IN SCHEMA *schema\_name*   <a name="grant-all-tables"></a>
Grants the specified permissions on all tables and views in the referenced schema\.

\( *column\_name* \[,\.\.\.\] \) ON TABLE *table\_name*   <a name="grant-column-level-privileges"></a>
Grants the specified permissions to users, groups, or PUBLIC on the specified columns of the Amazon Redshift table or view\.

\( *column\_list* \) ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="grant-external-table-column"></a>
Grants the specified permissions to an IAM role on the specified columns of the Lake Formation table in the referenced schema\.

ON EXTERNAL TABLE *schema\_name\.table\_name*   <a name="grant-external-table"></a>
Grants the specified permissions to an IAM role on the specified Lake Formation tables in the referenced schema\.

ON EXTERNAL SCHEMA *schema\_name*   <a name="grant-external-schema"></a>
Grants the specified permissions to an IAM role on the referenced schema\.

ON *iam\_role*   <a name="grant-iam_role"></a>
Grants the specified permissions to an IAM role\.

TO *username*   <a name="grant-to"></a>
Indicates the user receiving the permissions\.

TO IAM\_ROLE *iam\_role*   <a name="grant-to-iam-role"></a>
Indicates the IAM role receiving the permissions\.

WITH GRANT OPTION   <a name="grant-with-grant"></a>
Indicates that the user receiving the permissions can in turn grant the same permissions to others\. WITH GRANT OPTION can't be granted to a group or to PUBLIC\.

ROLE *role\_name*   <a name="grant-role"></a>
Grants the permissions to a role\.

GROUP *group\_name*   <a name="grant-group"></a>
Grants the permissions to a user group\. Can be a comma\-separated list to specify multiple user groups\.

PUBLIC   <a name="grant-public"></a>
Grants the specified permissions to all users, including users created later\. PUBLIC represents a group that always includes all users\. An individual user's permissions consist of the sum of permissions granted to PUBLIC, permissions granted to any groups that the user belongs to, and any permissions granted to the user individually\.  
Granting PUBLIC to a Lake Formation EXTERNAL TABLE results in granting the permission to the Lake Formation *everyone* group\.

CREATE   <a name="grant-create"></a>
Depending on the database object, grants the following permissions to the user or user group:  
+ For databases, CREATE allows users to create schemas within the database\.
+ For schemas, CREATE allows users to create objects within a schema\. To rename an object, the user must have the CREATE permission and own the object to be renamed\.
+ CREATE ON SCHEMA isn't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

TEMPORARY \| TEMP   <a name="grant-temporary"></a>
Grants the permission to create temporary tables in the specified database\. To run Amazon Redshift Spectrum queries, the database user must have permission to create temporary tables in the database\.   
By default, users are granted permission to create temporary tables by their automatic membership in the PUBLIC group\. To remove the permission for any users to create temporary tables, revoke the TEMP permission from the PUBLIC group\. Then explicitly grant the permission to create temporary tables to specific users or groups of users\.

ON DATABASE *db\_name*   <a name="grant-database"></a>
Grants the specified permissions on a database\.

USAGE   <a name="grant-usage"></a>
Grants USAGE permission on a specific schema, which makes objects in that schema accessible to users\. Specific actions on these objects must be granted separately \(for example, SELECT or UPDATE permission on tables\) for local Amazon Redshift schemas\. By default, all users have CREATE and USAGE permission on the PUBLIC schema\.   
 When you grant USAGE to external schemas using ON SCHEMA syntax, you don't need to grant actions separately on the objects in the external schema\. The corresponding catalog permissions control granular permissions on the external schema objects\. 

ON SCHEMA *schema\_name*   <a name="grant-schema"></a>
Grants the specified permissions on a schema\.  
GRANT CREATE ON SCHEMA and the CREATE permission in GRANT ALL ON SCHEMA aren't supported for Amazon Redshift Spectrum external schemas\. To grant usage of external tables in an external schema, grant USAGE ON SCHEMA to the users that need access\. Only the owner of an external schema or a superuser is permitted to create external tables in the external schema\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. 

EXECUTE ON ALL FUNCTIONS IN SCHEMA *schema\_name*  <a name="grant-all-functions"></a>
Grants the specified permissions on all functions in the referenced schema\.  
Amazon Redshift doesn't support GRANT or REVOKE statements for pg\_proc builtin entries defined in pg\_catalog namespace\. 

EXECUTE ON PROCEDURE *procedure\_name*   <a name="grant-procedure"></a>
Grants the EXECUTE permission on a specific stored procedure\. Because stored procedure names can be overloaded, you must include the argument list for the procedure\. For more information, see [Naming stored procedures](stored-procedure-naming.md)\.

EXECUTE ON ALL PROCEDURES IN SCHEMA *schema\_name*  <a name="grant-all-procedures"></a>
Grants the specified permissions on all stored procedures in the referenced schema\.

USAGE ON LANGUAGE *language\_name*   
Grants the USAGE permission on a language\.   
The USAGE ON LANGUAGE permission is required to create user\-defined functions \(UDFs\) by running the [CREATE FUNCTION](r_CREATE_FUNCTION.md) command\. For more information, see [UDF security and privileges](udf-security-and-privileges.md)\.   
The USAGE ON LANGUAGE permission is required to create stored procedures by running the [CREATE PROCEDURE](r_CREATE_PROCEDURE.md) command\. For more information, see [Security and privileges for stored procedures ](stored-procedure-security-and-privileges.md)\.  
For Python UDFs, use `plpythonu`\. For SQL UDFs, use `sql`\. For stored procedures, use `plpgsql`\.  
 

FOR \{ ALL \| COPY \| UNLOAD \| EXTERNAL FUNCTION \| CREATE MODEL \} \[, \.\.\.\]   <a name="grant-for"></a>
Specifies the SQL command for which the permission is granted\. You can specify ALL to grant the permission on the COPY, UNLOAD, EXTERNAL FUNCTION, and CREATE MODEL statements\. This clause applies only to granting the ASSUMEROLE permission\.

ALTER  
Grants the ALTER permission to users to add or remove objects from a datashare, or to set the property PUBLICACCESSIBLE\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

SHARE  
Grants pemrissions to users and user groups to add data consumers to a datashare\. This permission is required to enable the particular consumer \(account or namespace\) to access the datashare from their clusters\. The consumer can be the same or a different AWS account, with the same or a different cluster namespace as specified by a globally unique identifier \(GUID\)\.

ON DATASHARE *datashare\_name*   <a name="grant-datashare"></a>
Grants the specified permissions on the referenced datashare\. For information about consumer access control granularity, see [Sharing data at different levels in Amazon Redshift](granularity.md)\.

USAGE  
When USAGE is granted to a consumer account or namespace within the same account, the specific consumer account or namespace within the account can access the datashare and the objects of the datashare in read\-only fashion\.  

TO NAMESPACE 'clusternamespace GUID'  
Indicates a namespace in the same account where consumers can receive the specified permissions to the datashare\. Namespaces use a 128\-bit alphanumeric GUID\.

TO ACCOUNT 'accountnumber' \[ VIA DATA CATALOG \]  
Indicates the number of another account whose consumers can receive the specified permissions to the datashare\. Specifying ‘VIA DATA CATALOG’ indicates that you are granting usage of the datashare to a Lake Formation account\. Omitting this parameter means you're granting usage to an account that owns the cluster\.

ON DATABASE *shared\_database\_name> \[, \.\.\.\]*   <a name="grant-datashare"></a>
Grants the specified usage permissions on the specified database that is created in the specified datashare\.

ON SCHEMA* shared\_schema*   <a name="grant-datashare"></a>
Grants the specified permissions on the specified schema that is created in the specified datashare\.

CREATE MODEL  
Grants the CREATE MODEL permission to specific users or user groups\.

ON MODEL *model\_name*  
Grants the EXECUTE permission on a specific model\. 

\{ role \} \[, \.\.\.\]  
The role to be granted to another role, a user, or PUBLIC\.  
PUBLIC represents a group that always includes all users\. An individual user's permissions consist of the sum of permissions granted to PUBLIC, permissions granted to any groups that the user belongs to, and any permissions granted to the user individually\.

TO \{ \{ *user\_name* \[ WITH ADMIN OPTION \] \} \| role \}\[, \.\.\.\]  
Grants the specified role to a specified user with the WITH ADMIN OPTION, another role, or PUBLIC\.  
The WITH ADMIN OPTION clause provides the administration options for all the granted roles to all the grantees\. 

EXPLAIN RLS TO ROLE *rolename*  
Grants the permission to explain the row\-level security policy filters of a query in the EXPLAIN plan to a role\.

IGNORE RLS TO ROLE *rolename*   
Grants the permission to bypass row\-level security policies for a query to a role\.

## Usage notes<a name="r_GRANT-usage-notes-link"></a>

To learn more about the usage notes for GRANT, see [Usage notes](r_GRANT-usage-notes.md)\.

## Examples<a name="r_GRANT-examples-link"></a>

For examples of how to use GRANT, see [Examples](r_GRANT-examples.md)\.