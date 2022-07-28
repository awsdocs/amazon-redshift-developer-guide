# SVV\_LANGUAGE\_PRIVILEGES<a name="r_SVV_LANUGAGE_PRIVILEGES"></a>

Use SVV\_LANGUAGE\_PRIVILEGES to view the language permissions that are explicitly granted to users, roles, and groups in the current database\.

SVV\_LANGUAGE\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_LANUGAGE_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_LANUGAGE_PRIVILEGES.html)

## Sample query<a name="r_SVV_LANGUAGE_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_LANGUAGE\_PRIVILEGES\.

```
SELECT language_name,privilege_type,identity_name,identity_type,admin_option FROM svv_language_privileges
WHERE identity_name IN ('role1', 'reguser');

 language_name | privilege_type | identity_name | identity_type | admin_option
---------------+----------------+---------------+---------------+---------------
   exfunc      |     USAGE      |    reguser    |     user      |    False
   exfunc      |     USAGE      |     role1     |     role      |    False
   plpythonu   |     USAGE      |    reguser    |     user      |    False
```