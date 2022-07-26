# DROP ROLE<a name="r_DROP_ROLE"></a>

Removes a role from a database\. Only the role owner who either created the role, a user with the WITH ADMIN option, or a superuser can drop a role\.

You can't drop a role that is granted to a user or another role that is dependent on this role\.

## Required privileges<a name="r_DROP_ROLE-privileges"></a>

Following are the required privileges for DROP ROLE:
+ Superuser
+ Role owner who is either the user that created the role or a user that has been granted the role with the WITH ADMIN OPTION privilege\.

## Syntax<a name="r_DROP_ROLE-synopsis"></a>

```
DROP ROLE role_name [ FORCE | RESTRICT ] 
```

## Parameters<a name="r_DROP_ROLE-parameters"></a>

*role\_name*  
The name of the role\.

\[ FORCE \| RESTRICT \]  
The default setting is RESTRICT\. Amazon Redshift throws an error when you try to drop a role that has inherited another role\. Use FORCE to remove all role assignments, if any exists\. 

## Examples<a name="r_DROP_ROLE-examples"></a>

The following example drops the role `sample_role`\.

```
DROP ROLE sample_role FORCE;
```

The following example attempts to drop the role sample\_role1 that has been granted to a user with the default RESTRICT option\.

```
CREATE ROLE sample_role1;
GRANT sample_role1 TO user1;
DROP ROLE sample_role1;
ERROR:  cannot drop this role since it has been granted on a user
```

To successfully drop the sample\_role1 that has been granted to a user, use the FORCE option\.

```
DROP ROLE sample_role1 FORCE;
```

The following example attempts to drop the role sample\_role2 that has another role dependent on it with the default RESTRICT option\.

```
CREATE ROLE sample_role1;
CREATE ROLE sample_role2;
GRANT sample_role1 TO sample_role2;
DROP ROLE sample_role2;
ERROR:  cannot drop this role since it depends on another role
```

To successfully drop the sample\_role2 that has another role dependent on it, use the FORCE option\.

```
DROP ROLE sample_role2 FORCE;
```