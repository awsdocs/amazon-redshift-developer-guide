# SVV\_FUNCTION\_PRIVILEGES<a name="r_SVV_FUNCTION_PRIVILEGES"></a>

Use SVV\_RELATION\_PRIVILEGES to view the function permissions that are explicitly granted to users, roles, and groups in the current database\.

SVV\_FUNCTION\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_FUNCTION_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_FUNCTION_PRIVILEGES.html)

## Sample query<a name="r_SVV_FUNCTION_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_FUNCTION\_PRIVILEGES\.

```
SELECT namespace_name,function_name,argument_types,privilege_type,identity_name,identity_type,admin_option FROM svv_function_privileges
WHERE identity_name IN ('role1', 'reguser');

 namespace_name | function_name |       argument_types       | privilege_type |  identity_name | identity_type | admin_option
----------------+---------------+----------------------------+----------------+----------------+---------------+--------------
    public      | test_func1    | integer                    |    EXECUTE     |      role1     |     role      |  False
    public      | test_func2    | integer, character varying |    EXECUTE     |     reguser    |     user      |  False
```