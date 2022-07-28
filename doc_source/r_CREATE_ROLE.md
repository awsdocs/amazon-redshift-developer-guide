# CREATE ROLE<a name="r_CREATE_ROLE"></a>

Creates a new custom role that is a collection of privileges\.

## Required privileges<a name="r_CREATE_ROLE-privileges"></a>

Following are the required privileges for CREATE ROLE\.
+ Superuser
+ Users with the CREATE ROLE privilege

## Syntax<a name="r_CREATE_ROLE-synopsis"></a>

```
CREATE ROLE role_name
[ EXTERNALID external_id ]
```

## Parameters<a name="r_CREATE_ROLE-parameters"></a>

*role\_name*  
The name of the role\. The role name must be unique and can't be the same as any user names\. A role name can't be a reserved word\.  
A superuser or regular user with the CREATE ROLE privilege can create roles\. A user that is not a superuser but that has been granted USAGE to the role WITH GRANT OPTION and ALTER privilege can grant this role to anyone\.

EXTERNALID *external\_id*  
The identifier for the role, which is associated with an identity provider\. For more information, see [Native identity provider \(IdP\) federation for Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/redshift-iam-access-control-native-idp.html)\.

## Examples<a name="r_CREATE_ROLE-examples"></a>

The following example creates a role `sample_role1`\.

```
CREATE ROLE sample_role1;
```

The following example creates a role `sample_role1`, with an external ID that is associated with an identity provider\.

```
CREATE ROLE sample_role1 EXTERNALID "ABC123";
```