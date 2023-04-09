# USER\_IS\_MEMBER\_OF<a name="r_USER_IS_MEMBER_OF"></a>

Returns true if the user is a member of a role or group\. Superusers can check the membership of all users\. Regular users who are members of the sys:secadmin or sys:superuser role can check all users' membership\. Otherwise, regular users can only check themselves\. Amazon Redshift sends an error if the provided identities don't exist or the current user doesn't have access to the role\.

## Syntax<a name="r_USER_IS_MEMBER_OF-synopsis"></a>

```
user_is_member_of( user_name,  role_name | group_name)
```

## Arguments<a name="r_USER_IS_MEMBER_OF-arguments"></a>

 *user\_name*   
The name of the user\.

 *role\_name*   
The name of the role\.

 *group\_name*   
The name of the group\.

## Return type<a name="r_USER_IS_MEMBER_OF-return-type"></a>

Returns a BOOLEAN\.

## Example<a name="r_USER_IS_MEMBER_OF-example"></a>

The following query confirms that the user isn't a member of role1\.

```
SELECT user_is_member_of('reguser', 'role1');

 user_is_member_of
-------------------
           False
```