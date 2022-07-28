# SVV\_SCHEMA\_PRIVILEGES<a name="r_SVV_SCHEMA_PRIVILEGES"></a>

Use SVV\_SCHEMA\_PRIVILEGES to view the schema permissions that are explicitly granted to users, roles, and groups in the current database\.

SVV\_SCHEMA\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular user who has the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_SCHEMA_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_SCHEMA_PRIVILEGES.html)

## Sample query<a name="r_SVV_SCHEMA_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_SCHEMA\_PRIVILEGES\.

```
SELECT namespace_name,privilege_type,identity_name,identity_type,admin_option FROM svv_schema_privileges
WHERE namespace_name = 'test_schema1';

 namespace_name | privilege_type |  identity_name | identity_type | admin_option
----------------+----------------+----------------+---------------+--------------
 test_schema1   |    USAGE       |     reguser    |     user      |   False
 test_schema1   |    USAGE       |     role1      |     role      |   False
```