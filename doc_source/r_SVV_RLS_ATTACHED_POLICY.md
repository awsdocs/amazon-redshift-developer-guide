# SVV\_RLS\_ATTACHED\_POLICY<a name="r_SVV_RLS_ATTACHED_POLICY"></a>

Use SVV\_RLS\_ATTACHED\_POLICY to view a list of all relations and users that have one or more row\-level security policies attached on the currently connected database\.

Only users with the sys:secadmin role can query this view\.

## Table columns<a name="r_SVV_RLS_ATTACHED_POLICY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_RLS_ATTACHED_POLICY.html)

## Sample query<a name="r_SVV_RLS_ATTACHED_POLICY-sample-query"></a>

The following example displays the result of the SVV\_RLS\_ATTACHED\_POLICY\.

```
--Inspect the policy in SVV_RLS_ATTACHED_POLICY
SELECT * FROM svv_rls_attached_policy;

 relschema |        relname           | relkind |     polname     | grantor | grantee  | granteekind | is_pol_on | is_rls_on 
-----------+--------------------------+---------+-----------------+---------+----------+-------------+-----------+-----------
 public    | tickit_category_redshift |  table  | policy_concerts |   bob   |  analyst |    role     |    True   |   True
 public    | tickit_category_redshift |  table  | policy_concerts |   bob   |  dbadmin |    role     |    True   |   True
```