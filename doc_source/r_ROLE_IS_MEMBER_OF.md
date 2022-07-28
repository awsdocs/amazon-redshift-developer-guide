# ROLE\_IS\_MEMBER\_OF<a name="r_ROLE_IS_MEMBER_OF"></a>

Returns true if the role is a member of another role\. Superusers can check the membership of all roles\. Regular users who have the ACCESS SYSTEM TABLE permission can check all users' membership\. Otherwise, regular users can only check roles to which they have access\. Amazon Redshift errors out if the provided roles don't exist or the current user doesn't have access to the role\.

## Syntax<a name="r_ROLE_IS_MEMBER_OF-synopsis"></a>

```
role_is_member_of( role_name,  granted_role_name)
```

## Arguments<a name="r_ROLE_IS_MEMBER_OF-arguments"></a>

 *role\_name*   
The name of the role\.

 *granted\_role\_name*   
The name of the granted role\.

## Return type<a name="r_ROLE_IS_MEMBER_OF-return-type"></a>

Returns a BOOLEAN\.

## Example<a name="r_ROLE_IS_MEMBER_OF-example"></a>

The following query confirms that the role isn't a member of role1 nor role2\.

```
SELECT role_is_member_of('role1', 'role2');

 role_is_member_of
-------------------
             False
```