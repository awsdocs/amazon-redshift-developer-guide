# SVV\_ROLES<a name="r_SVV_ROLES"></a>

Use SVV\_ROLES to view a list of roles that a user has access to\.

SVV\_ROLES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_ROLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ROLES.html)

## Sample query<a name="r_SVV_ROLES-sample-query"></a>

The following example returns the output of SVV\_ROLES\.

```
SELECT role_name,role_owner FROM svv_roles WHERE role_name IN ('role1', 'role2');

 role_name | role_owner
-----------+------------
   role1   | superuser
   role2   | superuser
```