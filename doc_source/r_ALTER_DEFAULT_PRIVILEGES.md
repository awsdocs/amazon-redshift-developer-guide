# ALTER DEFAULT PRIVILEGES<a name="r_ALTER_DEFAULT_PRIVILEGES"></a>

Defines the default set of access privileges to be applied to objects that are created in the future by the specified user\. By default, users can change only their own default access privileges\. Only a superuser can specify default privileges for other users\.

You can apply default privileges to users or user groups\. You can set default privileges globally for all objects created in the current database, or for objects created only in the specified schemas\. 

Default privileges apply only to new objects\. Running ALTER DEFAULT PRIVILEGES doesn’t change privileges on existing objects\.

For more information about privileges, see [GRANT](r_GRANT.md)\.

To view information about the default privileges for database users, query the [PG\_DEFAULT\_ACL](r_PG_DEFAULT_ACL.md) system catalog table\. 

## Syntax<a name="r_ALTER_DEFAULT_PRIVILEGES-synopsis"></a>

```
ALTER DEFAULT PRIVILEGES
    [ FOR USER target_user [, ...] ]
    [ IN SCHEMA schema_name [, ...] ]
    grant_or_revoke_clause

where grant_or_revoke_clause is one of:

GRANT { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] } 
	ON TABLES 
	TO { user_name [ WITH GRANT OPTION ]| GROUP group_name | PUBLIC } [, ...]	 

GRANT { EXECUTE | ALL [ PRIVILEGES ] } 
	ON FUNCTIONS 
	TO { user_name [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
            
GRANT { EXECUTE | ALL [ PRIVILEGES ] } 
	ON PROCEDURES 
	TO { user_name [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]            

REVOKE [ GRANT OPTION FOR ] { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] } 
	ON TABLES 
	FROM user_name [, ...] [ CASCADE | RESTRICT ]

REVOKE  { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] } 
	ON TABLES 
	FROM { GROUP group_name | PUBLIC } [, ...] [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ] { EXECUTE | ALL [ PRIVILEGES ] } 
	ON FUNCTIONS 
	FROM user_name [, ...] [ CASCADE | RESTRICT ]

REVOKE { EXECUTE | ALL [ PRIVILEGES ] } 
	ON FUNCTIONS 
	FROM { GROUP group_name | PUBLIC } [, ...] [ CASCADE | RESTRICT ]       

REVOKE [ GRANT OPTION FOR ] { EXECUTE | ALL [ PRIVILEGES ] } 
	ON PROCEDURES 
	FROM user_name [, ...] [ CASCADE | RESTRICT ]            

REVOKE { EXECUTE | ALL [ PRIVILEGES ] } 
	ON PROCEDURES 
	FROM { GROUP group_name | PUBLIC } [, ...] [ CASCADE | RESTRICT ]
```

## Parameters<a name="r_ALTER_DEFAULT_PRIVILEGES-parameters"></a>

FOR USER *target\_user*  <a name="default-for-user"></a>
Optional\. The name of the user for which default privileges are defined\. Only a superuser can specify default privileges for other users\. The default value is the current user\.

IN SCHEMA *schema\_name*   <a name="default-in-schema"></a>
Optional\. If an IN SCHEMA clause appears, the specified default privileges are applied to new objects created in the specified *schema\_name*\. In this case, the user or user group that is the target of ALTER DEFAULT PRIVILEGES must have CREATE privilege for the specified schema\. Default privileges that are specific to a schema are added to existing global default privileges\. By default, default privileges are applied globally to the entire database\. 

GRANT   <a name="default-grant"></a>
The set of privileges to grant to the specified users or groups for all new tables, functions, or stored procedures created by the specified user\. You can set the same privileges and options with the GRANT clause that you can with the [GRANT](r_GRANT.md) command\. 

WITH GRANT OPTION   <a name="default-grant-option"></a>
A clause that indicates that the user receiving the privileges can in turn grant the same privileges to others\. You can't grant WITH GRANT OPTION to a group or to PUBLIC\. 

TO *user\_name* \| GROUP *group\_name*   <a name="default-to"></a>
The name of the user or user group to which the specified default privileges are applied\.

REVOKE   <a name="default-revoke"></a>
The set of privileges to revoke from the specified users or groups for all new tables, functions, or stored procedures created by the specified user\. You can set the same privileges and options with the REVOKE clause that you can with the [REVOKE](r_REVOKE.md) command\. 

GRANT OPTION FOR  <a name="default-revoke-option"></a>
 A clause that revokes only the option to grant a specified privilege to other users and doesn't revoke the privilege itself\. You can't revoke GRANT OPTION from a group or from PUBLIC\. 

FROM *user\_name* \| GROUP *group\_name*  <a name="default-from"></a>
The name of the user or user group from which the specified privileges are revoked by default\.

## Examples<a name="r_ALTER_DEFAULT_PRIVILEGES-examples"></a>

Suppose that you want to allow any user in the user group `report_readers` to view all tables created by the user `report_admin`\. In this case, execute the following command as a superuser\. 

```
alter default privileges for user report_admin grant select on tables to group report_readers; 
```

In the following example, the first command grants SELECT privileges on all new tables you create\. 

```
alter default privileges grant select on tables to public; 
```

The following example grants INSERT privilege to the `sales_admin` user group for all new tables and views that you create in the `sales` schema\. 

```
alter default privileges in schema sales grant insert on tables to group sales_admin; 
```

The following example reverses the ALTER DEFAULT PRIVILEGES command in the preceding example\. 

```
alter default privileges in schema sales revoke insert on tables from group sales_admin;
```

By default, the PUBLIC user group has EXECUTE permission for all new user\-defined functions\. To revoke `public` EXECUTE permissions for your new functions and then grant EXECUTE permission only to the `dev_test` user group, execute the following commands\. 

```
alter default privileges revoke execute on functions from public; 
alter default privileges grant execute on functions to group dev_test;
```