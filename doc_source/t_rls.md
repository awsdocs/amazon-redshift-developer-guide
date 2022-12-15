# Row\-level security<a name="t_rls"></a>

Using row\-level security \(RLS\) in Amazon Redshift, you can have granular access control over your sensitive data\. You can decide which users or roles can access specific records of data within schemas or tables, based on security policies that are defined at the database objects level\. In addition to column\-level security, where you can grant users permissions to a subset of columns, use RLS policies to further restrict access to particular rows of the visible columns\. For more information about column\-level security, see [Usage notes for column\-level access control](r_GRANT-usage-notes.md#r_GRANT-usage-notes-clp)\.

When you enforce RLS policies on tables, you can restrict returned result sets when users run queries\.

When creating RLS policies, you can specify expressions that dictate whether Amazon Redshift returns any existing rows in a table in a query\. By creating RLS policies to limit access, you don't have to add or externalize additional conditions in your queries\. 

When creating RLS policies, we recommend that you create simple policies and avoid complex statements in policies\. When defining RLS policies, don't use excessive table joins in the policy definition that are based on policies\.

When a policy refers to a lookup table, Amazon Redshift scans the additional table, in addition to the table on which the policy exists\. There will be performance differences between the same query for a user with an RLS policy attached, and a user without any policy attached\.

## Combining multiple policies per user<a name="t_rls_combine_policies"></a>

RLS in Amazon Redshift supports attaching multiple policies per user and object, given that all attached policies have the same table alias\. When there are multiple policies defined for a user, Amazon Redshift applies all the policies with the AND syntax\. Multiple policies on a table can be associated with you\. Either multiple policies are directly attached to you, or you belong to multiple roles, and the roles have different policies attached to them\. 