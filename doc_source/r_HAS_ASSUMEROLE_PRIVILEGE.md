# HAS\_ASSUMEROLE\_PRIVILEGE<a name="r_HAS_ASSUMEROLE_PRIVILEGE"></a>

The function returns Boolean `true` \(`t`\) if the user has the specified IAM role with the privilege to run the specified command or `false` \(`f`\) if the user does not have the specified IAM role with the privilege to run the specified command\. For more information about privileges, see [GRANT](r_GRANT.md)\. 

## Syntax<a name="r_HAS_ASSUMEROLE_PRIVILEGE-synopsis"></a>

```
has_assumerole_privilege( [ user, ] iam_role_arn, cmd_type)
```

## Arguments<a name="r_HAS_ASSUMEROLE_PRIVILEGE-arguments"></a>

 *user*   
The name of the user to check for IAM role privileges\. The default is to check the current user\. Superusers and users can use this function\. However, users can only view their own privileges\.

 *iam\_role\_arn*   
The role that has been granted the command privileges\. 

 *cmd\_type*   
The command for which access has been granted\. Valid values are the following:   
+ COPY
+ UNLOAD
+ EXTERNAL FUNCTION
+ CREATE MODEL

## Return type<a name="r_HAS_ASSUMEROLE_PRIVILEGE-return-type"></a>

BOOLEAN

## Example<a name="r_HAS_ASSUMEROLE_PRIVILEGE-example"></a>

The following query confirms that the user `reg_user1` has the privilege for the `Redshift-S3-Read` role to run the COPY command\.

```
select has_assumerole_privilege('reg_user1', 'arn:aws:iam::123456789012:role/Redshift-S3-Read', 'copy');
```

```
has_assumerole_privilege
------------------------
true
(1 row)
```