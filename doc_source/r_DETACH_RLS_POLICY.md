# DETACH RLS POLICY<a name="r_DETACH_RLS_POLICY"></a>

Detach a row\-level security policy on a table from one or more users or roles\.

Superusers and users or roles that have the `sys:secadmin` role can detach a policy\.

## Syntax<a name="r_DETACH_RLS_POLICY-synopsis"></a>

```
DETACH RLS POLICY policy_name ON [TABLE] table_name [, ...]
FROM { user_name | ROLE role_name | PUBLIC } [, ...]
```

## Parameters<a name="r_DETACH_RLS_POLICY-parameters"></a>

 *policy\_name*   
The name of the policy\.

ON \[TABLE\] *table\_name* \[, \.\.\.\]  
The table or view that the row\-level security policy is detached from\.

FROM \{ *user\_name* \| ROLE *role\_name* \| PUBLIC\} \[, \.\.\.\]  
Specifies whether the policy is detached from one or more specified users or roles\. 

## Usage notes<a name="r_DETACH_RLS_POLICY-usage"></a>

When working with the DETACH RLS POLICY statement, observe the following:
+ You can detach a policy from a relation, user, role, or public\.

## Examples<a name="r_DETACH_RLS_POLICY-examples"></a>

The following example detaches a policy on a table from a role\.

```
DETACH RLS POLICY policy_concerts ON tickit_category_redshift FROM ROLE analyst, ROLE dbadmin;
```