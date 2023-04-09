# RLS policy ownership and management<a name="t_rls_ownership"></a>

As a superuser, security administrator, or user that has the sys:secadmin role, you can create, modify, or manage all RLS policies for tables\. At the object level, you can turn row\-level security on or off without modifying the schema definition for tables\.

To get started with row\-level security, following are SQL statements that you can use:
+ Use the ALTER TABLE statement to turn on or off RLS on a table\. For more information, see [ALTER TABLE](r_ALTER_TABLE.md)\.
+ Use the CREATE RLS POLICY statement to create a security policy for one or more tables, and specify one or more users or roles in the policy\. 

  For more information, see [CREATE RLS POLICY](r_CREATE_RLS_POLICY.md)\.
+ Use the ATTACH RLS POLICY statement to attach a policy to one or more relations, to one or more users, or to roles\.

  For more information, see [ATTACH RLS POLICY ](r_ATTACH_RLS_POLICY.md)\.
+ Use the DETACH RLS POLICY statement to detach a policy from one or more relations, from one ore more users, or from roles\.

  For more information, see [DETACH RLS POLICY ](r_DETACH_RLS_POLICY.md)\.
+ Use the DROP RLS POLICY statement to drop a policy\.

  For more information, see [DROP RLS POLICY ](r_DROP_RLS_POLICY.md)\.
+ Use the GRANT and REVOKE statements to explicitly grant and revoke SELECT permissions to RLS policies that reference lookup tables\. For more information, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\. 

To monitor the policies created, sys:secadmin can view the [SVV\_RLS\_POLICY](r_SVV_RLS_POLICY.md) and [SVV\_RLS\_ATTACHED\_POLICY](r_SVV_RLS_ATTACHED_POLICY.md)\.

To list RLS\-protected relations, sys:secadmin can view SVV\_RLS\_RELATION\.

To trace the application of RLS policies on queries that reference RLS\-protected relations, a superuser, sys:operator, or any user with the system permission ACCESS SYSTEM TABLE can view [SVV\_RLS\_APPLIED\_POLICY ](r_SVV_RLS_APPLIED_POLICY.md)\. Note that sys:secadmin is not granted these permissions by default\.

To query tables with attached RLS policies, but not see them, you can grant the permission IGNORE RLS to any user\. Users that are superusers or sys:secadmin are automatically granted the permission IGNORE RLS\. For more information, see [GRANT](r_GRANT.md)\.

To explain the RLS policy filters of a query in the EXPLAIN plan to troubleshoot RLS\-related queries, you can grant the permission EXPLAIN RLS to any user\. For more information, see [GRANT](r_GRANT.md) and [EXPLAIN](r_EXPLAIN.md)\. 