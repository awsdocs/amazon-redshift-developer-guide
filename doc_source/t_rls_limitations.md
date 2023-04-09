# RLS policy limitations<a name="t_rls_limitations"></a>

Following are the limitations when working with RLS policies:
+ Amazon Redshift supports SELECT statements for certain RLS policies with lookups that have complex joins, but doesn't support UPDATE or DELETE statements\. In cases with UPDATE or DELETE statements, Amazon Redshift returns the following error:

  ```
  ERROR: One of the RLS policies on target relation is not supported in UPDATE/DELETE.
  ```
+ Whenever a user\-defined function \(UDF\) is referenced in a RLS policy of a relation attached to a user, the user must have the EXECUTE permission over the UDF to query the relation\.
+ Correlated subqueries aren't supported\. Amazon Redshift returns the following error:

  ```
  ERROR: RLS policy could not be rewritten.
  ```
+ RLS policies can't be applied to external tables\.
+ Amazon Redshift doesn't support datasharing with RLS\. If a relation doesn't have RLS turned off for datashares, the query fails on the consumer cluster with the following error:

  ```
  RLS-protected relation "rls_protected_table" cannot be accessed via datasharing query.
  ```
+ In cross\-database queries, Amazon Redshift blocks reads to RLS\-protected relations\. Users with the IGNORE RLS permission can access the protected relation using cross\-database queries\. When a user without the IGNORE RLS permission accesses RLS\-protected relation through a cross\-database query, the following error appears:

  ```
  RLS-protected relation "rls_protected_table" cannot be accessed via cross-database query.
  ```