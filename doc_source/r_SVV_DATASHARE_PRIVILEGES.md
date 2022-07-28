# SVV\_DATASHARE\_PRIVILEGES<a name="r_SVV_DATASHARE_PRIVILEGES"></a>

Use SVV\_DATASHARE\_PRIVILEGES to view the datashare permissions that are explicitly granted to users, roles, and groups in your Amazon Redshift cluster\.

SVV\_DATASHARE\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_DATASHARE_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_PRIVILEGES.html)

## Sample query<a name="r_SVV_DATASHARE_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_DATASHARE\_PRIVILEGES\.

```
SELECT datashare_name,privilege_type,identity_name,identity_type,admin_option FROM svv_datashare_privileges
WHERE datashare_name = 'demo_share';

 datashare_name | privilege_type |  identity_name | identity_type | admin_option
----------------+----------------+----------------+---------------+--------------
   demo_share   |     ALTER      |    superuser   |     user      |   False
   demo_share   |     ALTER      |    reguser     |     user      |   False
```