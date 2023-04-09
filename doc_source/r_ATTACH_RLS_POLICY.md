# ATTACH RLS POLICY<a name="r_ATTACH_RLS_POLICY"></a>

Attach a row\-level security policy on a table to one or more users or roles\.

Superusers and users or roles that have the `sys:secadmin` role can attach a policy\.

## Syntax<a name="r_ATTACH_RLS_POLICY-synopsis"></a>

```
ATTACH RLS POLICY policy_name ON [TABLE] table_name [, ...]
TO { user_name | ROLE role_name | PUBLIC } [, ...]
```

## Parameters<a name="r_ATTACH_RLS_POLICY-parameters"></a>

 *policy\_name*   
The name of the policy\.

ON \[TABLE\] *table\_name* \[, \.\.\.\]  
The table or view that the row\-level security policy is attached to\.

TO \{ *user\_name* \| ROLE *role\_name* \|  PUBLIC\} \[, \.\.\.\]  
Specifies whether the policy is attached to one or more specified users or roles\. 

## Usage notes<a name="r_ATTACH_RLS_POLICY-usage"></a>

When working with the ATTACH RLS POLICY statement, observe the following:
+ The table being attached should have all the columns listed in the WITH clause of the policy creation statement\.
+ Amazon Redshift RLS doesn't support attaching RLS policies to the following objects: catalog tables, cross\-database relations, external tables, regular views, late\-binding views, temporary tables, and lookup tables for RLS policies\.
+ You can't attach a RLS policy to superusers or to a sys:secadmin\.

## Examples<a name="r_ALTER_RLS_POLICY-examples"></a>

The following example attaches a policy on a table to a role\.

```
ATTACH RLS POLICY policy_concerts ON tickit_category_redshift TO ROLE analyst, ROLE dbadmin;
```