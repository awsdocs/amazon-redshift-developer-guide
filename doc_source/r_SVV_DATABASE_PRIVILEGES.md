# SVV\_DATABASE\_PRIVILEGES<a name="r_SVV_DATABASE_PRIVILEGES"></a>

Use SVV\_DATABASE\_PRIVILEGES to view the database permissions that are explicitly granted to users, roles, and groups in your Amazon Redshift cluster\.

SVV\_DATABASE\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_DATABASE_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATABASE_PRIVILEGES.html)

## Sample query<a name="r_SVV_DATABASE_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_DATABASE\_PRIVILEGES\.

```
SELECT database_name,privilege_type,identity_name,identity_type,admin_option FROM svv_database_privileges
WHERE database_name = 'test_db';

 database_name | privilege_type | identity_name | identity_type | admin_option
---------------+----------------+---------------+---------------+--------------
     test_db   |     CREATE     |     reguser   |      user     |     False
     test_db   |     CREATE     |      role1    |      role     |     False
     test_db   |     TEMP       |      public   |      public   |     False
     test_db   |     TEMP       |      role1    |      role     |     False
```