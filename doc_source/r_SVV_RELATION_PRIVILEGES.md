# SVV\_RELATION\_PRIVILEGES<a name="r_SVV_RELATION_PRIVILEGES"></a>

Use SVV\_RELATION\_PRIVILEGES to view the relation \(tables and views\) permissions that are explicitly granted to users, roles, and groups in the current database\.

SVV\_RELATION\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_RELATION_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_RELATION_PRIVILEGES.html)

## Sample query<a name="r_SVV_RELATION_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_RELATION\_PRIVILEGES\.

```
SELECT namespace_name,relation_name,privilege_type,identity_name,identity_type,admin_option FROM svv_relation_privileges
WHERE relation_name = 'orders' AND privilege_type = 'SELECT';

 namespace_name | relation_name | privilege_type |  identity_name | identity_type | admin_option
----------------+---------------+----------------+----------------+---------------+--------------
     public     |    orders     |     SELECT     |    reguser     |     user      |    False
     public     |    orders     |     SELECT     |     role1      |     role      |    False
```