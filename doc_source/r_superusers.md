# Superusers<a name="r_superusers"></a>

<a name="def_superusers"></a>Database superusers have the same privileges as database owners for all databases\.

The *masteruser*, which is the user you created when you launched the cluster, is a superuser\.

You must be a superuser to create a superuser\.

Amazon Redshift system tables and system views are either visible only to superusers or visible to all users\. Only superusers can query system tables and system views that are designated "visible to superusers\." For information, see [System tables and views](c_intro_system_tables.md)\.

Superusers can view all PostgreSQL catalog tables\. For information, see [System catalog tables](c_intro_catalog_views.md)\.

A database superuser bypasses all permission checks\. Be very careful when using a superuser role\. We recommend that you do most of your work as a role that is not a superuser\. Superusers retain all privileges regardless of GRANT and REVOKE commands\.

To create a new database superuser, log on to the database as a superuser and issue a CREATE USER command or an ALTER USER command with the CREATEUSER privilege\.

```
create user adminuser createuser password '1234Admin';
alter user adminuser createuser;
```