# SVV\_ROLE\_GRANTS<a name="r_SVV_ROLE_GRANTS"></a>

Use SVV\_ROLE\_GRANTS to view a list of roles that are explicitly granted roles in the cluster\.

SVV\_ROLE\_GRANTS is visible to all users\. Superusers can see all rows\. Regular user who has the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see roles to which they have access or if they are the role owner\.

## Table columns<a name="r_SVV_ROLE_GRANTS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ROLE_GRANTS.html)

## Sample query<a name="r_SVV_ROLE_GRANTS-sample-query"></a>

The following example returns the output of SVV\_ROLE\_GRANTS\.

```
GRANT ROLE role1 TO ROLE role2;
GRANT ROLE role2 TO ROLE role3;

SELECT role_name, granted_role_name FROM svv_role_grants;

 role_name |  granted_role_name
-----------+--------------------
   role2   |      role1
   role3   |      role2
(2 rows)
```