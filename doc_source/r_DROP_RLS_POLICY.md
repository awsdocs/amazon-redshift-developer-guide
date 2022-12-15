# DROP RLS POLICY<a name="r_DROP_RLS_POLICY"></a>

Drops a row\-level security policy for all tables in all databases\.

Superusers and users or roles that have the sys:secadmin role can drop a policy\.

## Syntax<a name="r_DROP_RLS_POLICY-synopsis"></a>

```
DROP RLS POLICY [ IF EXISTS ] policy_name [ CASCADE | RESTRICT ]
```

## Parameters<a name="r_DROP_RLS_POLICY-parameters"></a>

 *IF EXISTS*   
A clause that indicates if the specified policy already exists\.

 *policy\_name*   
The name of the policy\.

 *CASCADE*   
A clause that indicates to automatically detach the policy from all attached tables before dropping the policy\.

 *RESTRICT*   
A clause that indicates not to drop the policy when it is attached to some tables\. This is the default\.

## Examples<a name="r_DROP_RLS_POLICY-examples"></a>

The following example drops the row\-level security policy\.

```
DROP RLS POLICY policy_concerts;
```