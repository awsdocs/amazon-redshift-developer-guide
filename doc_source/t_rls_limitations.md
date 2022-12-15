# RLS policy limitations<a name="t_rls_limitations"></a>

Following are the limitations when working with RLS policies:
+ Amazon Redshift supports SELECT statements for certain RLS policies with lookups that have complex joins, but doesn't support UPDATE or DELETE statements\. In cases with UPDATE or DELETE statements, Amazon Redshift returns the following error:

  ```
  ERROR: One of the RLS policies on target relation is not supported in UPDATE/DELETE.
  ```
+ RLS policies can only run user\-defined functions \(UDFs\) that have been granted the EXECUTE permission to PUBLIC\.
+ Correlated subqueries aren't supported\. Amazon Redshift returns the following error:

  ```
  ERROR: RLS policy could not be rewritten.
  ```
+ RLS policies can't be applied to any external tables\.
+ Amazon Redshift doesn't support datasharing with RLS\. Amazon Redshift RLS policies aren't applied to relations that are accessed through datashares\. Users with read access to producer objects on the consumer cluster will see all rows in the table irrespective of RLS policies defined on the producer cluster\.
+ In cross\-database queries, Amazon Redshift blocks reads to RLS\-protected relations\. Users with the IGNORE RLS permission can access the protected relation using cross\-database queries\. When a user without the IGNORE RLS permission accesses RLS\-protected relation through a cross\-database query, the following error appears:

  ```
  RLS-protected relation "rls_protected_table" cannot be accessed via cross-database query.
  ```