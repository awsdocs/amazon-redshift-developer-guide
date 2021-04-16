# PG\_GET\_GRANTEE\_BY\_IAM\_ROLE<a name="PG_GET_GRANTEE_BY_IAMROLE"></a>

Returns all users and groups granted a specified IAM role\.

## Syntax<a name="PG_GET_GRANTEE_BY_IAMROLE-synopsis"></a>

```
pg_get_grantee_by_iam_role('iam_role_arn')
```

## Arguments<a name="PG_GET_GRANTEE_BY_IAMROLE-arguments"></a>

 *iam\_role\_arn*   
The IAM role for which to return the users and groups that have been granted this role\.

## Return type<a name="PG_GET_GRANTEE_BY_IAMROLE-return-type"></a>

VARCHAR 

## Usage notes<a name="PG_GET_GRANTEE_BY_IAMROLE-usage-notes"></a>

The PG\_GET\_GRANTEE\_BY\_IAM\_ROLE function returns one row for each user or group\. Each row contains the grantee name, grantee type, and granted privilege\. The possible values for the grantee type are `p` for public, `u` for user, and `g` for group\. 

You must be a superuser to use this function\.

## Example<a name="PG_GET_GRANTEE_BY_IAMROLE-example"></a>

The following example indicates that the IAM role `Redshift-S3-Write` is granted to `group1` and `reg_user1`\. Users in `group_1` can specify the role only for COPY operations, and user `reg_user1` can specify the role only to perform UNLOAD operations\.

```
select pg_get_grantee_by_iam_role('arn:aws:iam::123456789012:role/Redshift-S3-Write');
```

```
  pg_get_grantee_by_iam_role
-----------------------------
 (group_1,g,COPY)
 (reg_user1,u,UNLOAD)
```

The following example of the PG\_GET\_GRANTEE\_BY\_IAM\_ROLE function formats the result as a table\.

```
select grantee, grantee_type, cmd_type FROM pg_get_grantee_by_iam_role('arn:aws:iam::123456789012:role/Redshift-S3-Write') res_grantee(grantee text, grantee_type text, cmd_type text) ORDER BY 1,2,3;
```

```
  grantee  | grantee_type | cmd_type
-----------+--------------+----------
 group_1   | g            | COPY
 reg_user1 | u            | UNLOAD
```