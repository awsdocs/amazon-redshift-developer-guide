# Users<a name="r_Users"></a>

You can create and manage database users using the Amazon Redshift SQL commands CREATE USER and ALTER USER, or you can configure your SQL client with custom Amazon Redshift JDBC or ODBC drivers that manage the process of creating database users and temporary passwords as part of the database logon process\.

The drivers authenticate database users based on AWS Identity and Access Management \(IAM\) authentication\. If you already manage user identities outside of AWS, you can use a SAML 2\.0\-compliant identity provider \(IdP\) to manage access to Amazon Redshift resources\. You use an IAM role to configure your IdP and AWS to permit your federated users to generate temporary database credentials and log on to Amazon Redshift databases\. For more information, see [Using IAM authentication to generate database user credentials](https://docs.aws.amazon.com/redshift/latest/mgmt/generating-user-credentials.html)\. 

Amazon Redshift user accounts can only be created and dropped by a database superuser\. Users are authenticated when they login to Amazon Redshift\. They can own databases and database objects \(for example, tables\) and can grant privileges on those objects to users, groups, and schemas to control who has access to which object\. Users with CREATE DATABASE rights can create databases and grant privileges to those databases\. Superusers have database ownership privileges for all databases\.

## Creating, altering, and deleting users<a name="r_Users-creatingaltering-and-deleting-users"></a>

Database users accounts are global across a data warehouse cluster \(and not per individual database\)\. 
+  To create a user use the [CREATE USER](r_CREATE_USER.md) command\. 
+  To create a superuser use the [CREATE USER](r_CREATE_USER.md) command with the CREATEUSER option\. 
+ To remove an existing user, use the [DROP USER](r_DROP_USER.md) command\. 
+ To make changes to a user account, such as changing a password, use the [ALTER USER](r_ALTER_USER.md) command\. 
+ To view a list of users, query the PG\_USER catalog table:

  ```
  select * from pg_user;
  
    usename   | usesysid | usecreatedb | usesuper | usecatupd |  passwd  | valuntil | useconfig
  ------------+----------+-------------+----------+-----------+----------+----------+-----------
   rdsdb      |        1 | t           | t        | t         | ******** |          |
   masteruser |      100 | t           | t        | f         | ******** |          |
   dwuser     |      101 | f           | f        | f         | ******** |          |
   simpleuser |      102 | f           | f        | f         | ******** |          |
   poweruser  |      103 | f           | t        | f         | ******** |          |
   dbuser     |      104 | t           | f        | f         | ******** |          |
  (6 rows)
  ```