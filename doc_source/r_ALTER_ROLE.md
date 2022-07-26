# ALTER ROLE<a name="r_ALTER_ROLE"></a>

Renames a role or changes the owner\.

## Required privileges<a name="r_ALTER_ROLE-privileges"></a>

Following are the required privileges for ALTER ROLE:
+ Superuser
+ Users with the ALTER ROLE privilege

## Syntax<a name="r_ALTER_ROLE-synopsis"></a>

```
ALTER ROLE role [ WITH ]
  { { RENAME TO role } | { OWNER TO user_name } }[, ...]
  [ EXTERNALID TO external_id ]
```

## Parameters<a name="r_ALTER_ROLE-parameters"></a>

 *role*   
The name of the role to be altered\.

RENAME TO  
A new name for the role\.

OWNER TO *user\_name*  
A new owner for the role\. 

EXTERNALID TO *external\_id*  
A new external ID for the role, which is associated with an identity provider\. For more information, see [Native identity provider \(IdP\) federation for Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/redshift-iam-access-control-native-idp.html)\.

## Examples<a name="r_ALTER_ROLE-examples"></a>

The following example changes the name of a role from `sample_role1` to `sample_role2`\.

```
ALTER ROLE sample_role1 WITH RENAME TO sample_role2;
```

The following example changes the owner of the role\.

```
ALTER ROLE sample_role1 WITH OWNER TO user1
```

The syntax of the ALTER ROLE is similar to ALTER PROCEDURE following\.

```
ALTER PROCEDURE first_quarter_revenue(bigint, numeric) RENAME TO quarterly_revenue;
```

The following example changes the owner of a procedure to `etl_user`\.

```
ALTER PROCEDURE quarterly_revenue(bigint, numeric) OWNER TO etl_user;
```

The following example updates a role `sample_role1` with a new external ID that is associated with an identity provider\.

```
ALTER ROLE sample_role1 EXTERNALID TO "XYZ456";
```