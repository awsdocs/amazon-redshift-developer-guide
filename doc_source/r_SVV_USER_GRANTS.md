# SVV\_USER\_GRANTS<a name="r_SVV_USER_GRANTS"></a>

Use SVV\_USER\_GRANTS to view the list of users that are explicitly granted roles in the cluster\.

SVV\_USER\_GRANTS is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can see only current users' explicitly granted roles\.

## Table columns<a name="sub-r_SVV_USER_GRANTS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_USER_GRANTS.html)

## Sample queries<a name="r_SVV_USER_GRANTS-sample-queries"></a>

The following queries grant roles to users and show the list of users that are explicitly granted roles\.

```
GRANT ROLE role1 TO reguser;
GRANT ROLE role2 TO reguser;
GRANT ROLE role1 TO superuser;
GRANT ROLE role2 TO superuser;

SELECT user_name,role_name,admin_option FROM svv_user_grants;

 user_name | role_name | admin_option
-----------+-----------+--------------
 superuser |  role1    | False
 reguser   |  role1    | False
 superuser |  role2    | False
  reguser  |  role2    | False
```