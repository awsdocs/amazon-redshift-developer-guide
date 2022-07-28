# SVV\_SYSTEM\_PRIVILEGES<a name="r_SVV_SYSTEM_PRIVILEGES"></a>

SVV\_SYSTEM\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular user who has the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_SYSTEM_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_SYSTEM_PRIVILEGES.html)

## Sample query<a name="r_SVV_SYSTEM_PRIVILEGES-sample-query"></a>

The following example displays the result for the specified parameters\.

```
SELECT system_privilege,identity_name,identity_type FROM svv_system_privileges
WHERE system_privilege = 'ALTER TABLE' AND identity_name = 'sys:superuser';

 system_privilege | identity_name | identity_type
------------------+---------------+---------------
   ALTER TABLE    | sys:superuser |     role
```