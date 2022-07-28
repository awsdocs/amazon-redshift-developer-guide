# Row\-level security<a name="t_rls"></a>

Using row\-level security \(RLS\) in Amazon Redshift, you can have granular access control over your sensitive data\. You can decide which users or roles can access specific records of data within schemas or tables, based on security policies that are defined at the database objects level\. In addition to column\-level security, where you can grant users permissions to a subset of columns, use RLS policies to further restrict access to particular rows of the visible columns\. For more information about column\-level security, see [Usage notes for column\-level access control](r_GRANT-usage-notes.md#r_GRANT-usage-notes-clp)\.

When you enforce RLS policies on tables, you can restrict returned result sets when users run queries\.

When creating RLS policies, you can specify expressions that dictate whether Amazon Redshift returns any existing rows in a table in a query\. By creating RLS policies to limit access, you don't have to add or externalize additional conditions in your queries\. 

When creating RLS policies, we recommend that you create simple policies and avoid complex statements in policies\. When defining RLS policies, don't use excessive table joins in the policy definition that are based on policies\.

When a policy refers to a lookup table, Amazon Redshift scans the additional table in addition to the table on which the policy exists\. There will be performance differences between the same query for a user with an RLS policy attached and a user without any policy attached\.

## Using RLS policies in SQL statements<a name="t_rls_statements"></a>

When using RLS policies in SQL statements, Amazon Redshift applies the following rules:
+ Amazon Redshift applies RLS policies to the SELECT, UPDATE, and DELETE statements by default\. 
+ For SELECT and UNLOAD, Amazon Redshift filters rows according to your defined policy\.
+ For UPDATE, Amazon Redshift updates only the rows that are visible to you\. If a policy restricts a subset of the rows in a table, then you can't update them\.
+ For DELETE, you can delete only the rows that are visible to you\. If a policy restricts a subset of the rows in a table, then you can't delete them\. For TRUNCATE, you can still truncate the table\.
+ For CREATE TABLE LIKE, tables created with the LIKE options won't inherit permission settings from the source table\. Similarly, the target table won't inherit the RLS policies from source table\.

## Combining multiple policies per user<a name="t_rls_combine_policies"></a>

RLS in Amazon Redshift supports attaching multiple policies per user and object, given that all attached policies have the same table alias\. When there are multiple policies defined for a user, Amazon Redshift applies all the policies with the AND syntax\. Multiple policies on a table can be associated with you\. Either multiple policies are directly attached to you, or you belong to multiple roles, and the roles have different policies attached to them\. 

## RLS policy ownership and management<a name="t_rls_ownership"></a>

As a superuser, security administrator, or user that has the sys:secadmin role, you can create, modify, or manage all RLS policies for tables\. At the object level, you can turn row\-level security on or off without modifying the schema definition for tables\.

To get started with row\-level security, following are SQL statements that you can use:
+ Use the ALTER TABLE statement to turn on or off RLS on a table\. For more information, see [ALTER TABLE](r_ALTER_TABLE.md)\.
+ Use the CREATE RLS POLICY statement to create a security policy for one or more tables, and specify one or more users or roles in the policy\. 

  For more information, see [CREATE RLS POLICY ](r_CREATE_RLS_POLICY.md)\.
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

## Policy\-dependent objects and principles<a name="t_rls_object_dependency"></a>

To ensure security for any applications and to prevent policy objects from becoming stale or invalid, Amazon Redshift doesn't permit dropping or altering objects referenced by RLS policies\.

The following example illustrates how schema dependency is being tracked\.

```
-- The CREATE and ATTACH policy statements for `policy_events` references some
-- target and lookup tables.
-- Target tables are tickit_event_redshift and target_schema.target_event_table.
-- Lookup table is tickit_sales_redshift.
-- Policy `policy_events` has following dependencies:
--   table tickit_sales_redshift column eventid, qtysold
--   table tickit_event_redshift column eventid
--   table target_event_table column eventid
--   schema public and target_schema
CREATE RLS POLICY policy_events
WITH (eventid INTEGER)
USING (
    eventid IN (SELECT eventid FROM tickit_sales_redshift WHERE qtysold <3)
);

ATTACH RLS POLICY policy_events ON tickit_event_redshift TO ROLE analyst;

ATTACH RLS POLICY policy_events ON target_schema.target_event_table TO ROLE consumer;
```

Following lists schema object dependencies that Amazon Redshift tracks for RLS policies\.
+ When tracking schema object dependency for the target table, Amazon Redshift follows these rules:
  + Amazon Redshift detaches the policy from a relation, user, role, or public when you drop a target table\.
  + When you rename a target table name, there is no impact to the attached policies\.
  + You can only drop the columns of the target table referenced inside the policy definition if you drop or detach the policy first\. This also applies when the CASCADE option is specified\. You can drop other columns in the target table\.
  + You can't rename the referred columns of the target table\. To rename referred columns, detach the policy first\. This also applies when the CASCADE option is specified\.
  + You can't change the type of the referred column, even when you specify the CASCADE option\.
+ When tracking schema object dependency for the lookup table, Amazon Redshift follows these rules:
  + You can't drop a lookup table\. To drop a lookup table, first drop the policy in which the lookup table is referred\.
  + You can't rename a lookup table\. To rename a lookup table, first drop the policy in which the lookup table is referred\. This also applies when the CASCADE option is specified\.
  + You can't drop the lookup table columns used in the policy definition\. To drop the lookup table columns used in the policy definition, first drop the policy in which the lookup table is referred\. This also applies when the CASCADE option is specified in the ALTER TABLE DROP COLUMN statement\. You can drop other columns in the lookup table\.
  + You can't rename the referred columns of the lookup table\. To rename referred columns, first drop the policy in which the lookup table is referred\. This also applies when the CASCADE option is specified\.
  + You can't change the type of the referred column\.
+ When a user or role is dropped, Amazon Redshift detaches all policies attached to the user or role automatically\.
+ When you use the CASCADE option in the DROP SCHEMA statement, Amazon Redshift also drops the relations in the schema\. It also drops the relations in any other schemas that are dependent on relations in the dropped schema\. For a relation that is a lookup table in a policy, Amazon Redshift fails the DROP SCHEMA DDL\. For any relations dropped by the DROP SCHEMA statement, Amazon Redshift detaches all policies that are attached to those relations\.
+ You can only drop a lookup function \(a function that is referred inside a policy definition\) when you also drop the policy\. This also applies when the CASCADE option is specified\.
+ When a policy is attached to a table, Amazon Redshift checks if this table is a lookup table in a different policy\. If this is the case, Amazon Redshift won't allow attaching a policy to this table\.
+ While creating an RLS policy, Amazon Redshift checks if this table is a target table for any other RLS policy\. If this is the case, Amazon Redshift won't allow creating a policy on this table\.

## RLS policy usage notes<a name="t_rls_usage"></a>

Following are considerations for working with RLS policies:
+ Amazon Redshift applies RLS policies to SELECT, UPDATE, or DELETE statements\.
+ Amazon Redshift doesn't apply RLS policies to INSERT, COPY, ALTER TABLE APPEND statements\.
+ Row\-level security works with column\-level security to protect your data\.
+ When your Amazon Redshift cluster was on the latest generally available version that supports RLS, but is downgraded to an earlier version, Amazon Redshift returns an error when you run a query on base tables with RLS policies attached\. The sys:secadmin can revoke access from users that were granted restricted policies, turn off RLS on tables, and drop the policies\.
+ When RLS is turned on for the source relation, Amazon Redshift supports the ALTER TABLE APPEND statement for superusers, users that have been explicitly granted the system privilege IGNORE RLS, or the sys:secadmin role\. In this case, you can run the ALTER TABLE APPEND statement to append rows to a target table by moving data from an existing source table\. Amazon Redshift moves all tuples from the source relation into the target relation\. The RLS status of the target relation doesn't affect the ALTER TABLE APPEND statement\.
+ To facilitate migration from other data warehouse systems, you can set and retrieve customized session context variables for a connection by specifying the variable name and value\.

  The following example sets session context variables for a row\-level security \(RLS\) policy\.

  ```
  -- Set a customized context variable.
  SELECT set_config(‘app.category’, ‘Concerts’, FALSE);
  
  -- Create a RLS policy using current_setting() to get the value of a customized context variable.
  CREATE RLS POLICY policy_categories
  WITH (catgroup VARCHAR(10)) 
  USING (catgroup = current_setting('app.category', FALSE));
  
  -- Set correct roles and attach the policy on the target table to one or more roles.
  ATTACH RLS POLICY policy_categories ON tickit_category_redshift TO ROLE analyst, ROLE dbadmin;
  ```

  For details on how to set and retrieve customized session context variables, see [SET](r_SET.md), [SET\_CONFIG](r_SET_CONFIG.md), [SHOW](r_SHOW.md), [CURRENT\_SETTING](r_CURRENT_SETTING.md), and [RESET](r_RESET.md)\.
+ You can't run SET SESSION AUTHORIZATION statements between DECLARE and FETCH statements on cursors referencing RLS\-protected tables\. Amazon Redshift won't update RLS policies on the cursor after a SET SESSION AUTHORIZATION statement\.

## Best practices for RLS performance<a name="t_rls_performance"></a>

Following are best practices to ensure better performance from Amazon Redshift on tables protected by RLS\.

### Safety of operators and functions<a name="t_rls_safe_operators"></a>

When querying RLS\-protected tables, the usage of certain operators or functions may lead to performance degradation\. Amazon Redshift classifies operators and functions either as safe or unsafe for querying RLS\-protected tables\. A function or operator is classified as RLS\-safe when it doesn't have any observable side\-effects depending on the inputs\. In particular, a RLS\-safe function or operator can't be one of the following:
+ Outputs an input value, or any value that is dependent on the input value, with or without an error message\.
+ Fails or returns errors that are dependent on the input value\.

RLS\-unsafe operators include:
+ Arithmetic operators — \+, \-, /, \*, %\.
+ Text operators — LIKE and SIMILAR TO\.
+ Cast operators\.
+ UDFs\.

Use the following SELECT statement to check the safety of operators and functions\.

```
SELECT proname, proc_is_rls_safe(oid) FROM pg_proc;
```

Amazon Redshift imposes restrictions on the order of evaluation of user predicates containing RLS\-unsafe operators and functions when planning queries on RLS\-protected tables\. Queries referencing RLS\-unsafe operators or functions might cause performance degradation when querying RLS\-protected tables\. Performance can degrade significantly when Amazon Redshift can't push RLS\-unsafe predicates down to base table scans to take advantage of sort keys\. For better performance, avoid queries using RLS\-unsafe predicates that take advantage of a sort key\. To verify that Amazon Redshift is able to push down operators and functions, you can use EXPLAIN statements in combination with the system privilege EXPLAIN RLS\.

### Using AQUA with RLS policies<a name="t_rls_aqua"></a>

Amazon Redshift doesn't use AQUA to accelerate queries on RLS\-protected tables because operators LIKE and SIMILAR TO are classified as RLS\-unsafe\.

For best practices on how to choose the best sort key to store your data in sorted orders, see [Choose the best sort key](c_best-practices-sort-key.md)\.

For information on how to use EXPLAIN for RLS policies, see [Using EXPLAIN for RLS ](r_EXPLAIN.md#r_EXPLAIN-RLS)\.

For information on when Amazon Redshift use AQUA to run queries, see [When does Amazon Redshift use AQUA to run queries?](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-cluster-aqua-understanding.html) in the *Amazon Redshift Cluster Management Guide*

### Result caching<a name="t_rls_result_cache"></a>

To reduce query execution time and improve system performance, Amazon Redshift caches the results of certain types of queries in the memory on the leader node\.

Amazon Redshift uses cached results for a new query scanning RLS\-protected tables when all the conditions for unprotected tables are true and when all of the following are true:
+ The tables or views in the policy haven't been modified\.
+ The policy doesn't use a function that must be evaluated each time it's run, such as GETDATE or CURRENT\_USER\.

For better performance, avoid using policy predicates that don't satisfy the above conditions\.

For more information on result caching in Amazon Redshift, see [ Result caching ](c_challenges_achieving_high_performance_queries.md#result-caching)\.

### Complex policies<a name="t_rls_complex_policies"></a>

For better performance, avoid using complex policies with subqueries that join multiple tables\.

## RLS policy limitations<a name="t_rls_limitations"></a>

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

## Downgrading or restoring to an Amazon Redshift cluster version that doesn't support RLS<a name="t_rls_downgrade"></a>

Once a relation is protected with an RLS policy, it is only accessible by authorized users or roles\. When a cluster is downgraded to an Amazon Redshift cluster version where RLS is unsupported or unavailable, only superusers can access RLS\-protected relations\.

When you perform a restore from the current track that supports RLS to a trailing track that doesn't support RLS, only superusers can access the protected relations\.

We recommend that you upgrade your cluster to a version that supports RLS to use all the RLS functionalities\. When RLS is unavailable, Amazon Redshift can't enforce consistency of policy\-dependent objects\. Avoid making any schema changes until RLS becomes available\.

While RLS is unavailable, superusers can temporarily restore access to other users who have the sys:secadmin system role, or the system permission IGNORE RLS\. Superus ers can first revoke the SELECT permission from all users or roles that don't have the sys:secadmin or system permission IGNORE RLS\. Subsequently, superusers can turn off RLS with ALTER TABLE\. 

## Creating, attaching, detaching, and dropping RLS policies<a name="r_rls-procedures"></a>

You can perform the following actions:
+ To create an RLS policy, use the [CREATE RLS POLICY ](r_CREATE_RLS_POLICY.md) command\.
+ To attach an RLS policy on a table to one or more users or roles, use the [ATTACH RLS POLICY ](r_ATTACH_RLS_POLICY.md) command\.
+ To detach a row\-level security policy on a table from one or more users or roles, use the [DETACH RLS POLICY ](r_DETACH_RLS_POLICY.md) command\.
+ To drop an RLS policy for all tables in all databases, use the [DROP RLS POLICY ](r_DROP_RLS_POLICY.md) command\.

The following is an end\-to\-end example to illustrate how a superuser creates some users and roles\. Then, a user with the secadmin role creates, attaches, detaches, and drops RLS policies\. This example uses the tickit sample database\. For more information, see [Load data from Amazon S3 to Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-create-sample-db.html) in the *Amazon Redshift Getting Started Guide*\.

```
-- Create users and roles referenced in the policy statements.
CREATE ROLE analyst;
CREATE ROLE consumer;
CREATE ROLE dbadmin;
CREATE ROLE auditor;
CREATE USER bob WITH PASSWORD 'Name_is_bob_1';
CREATE USER alice WITH PASSWORD 'Name_is_alice_1';
CREATE USER joe WITH PASSWORD 'Name_is_joe_1';
CREATE USER molly WITH PASSWORD 'Name_is_molly_1';
CREATE USER bruce WITH PASSWORD 'Name_is_bruce_1';
GRANT ROLE sys:secadmin TO bob;
GRANT ROLE analyst TO alice;
GRANT ROLE consumer TO joe;
GRANT ROLE dbadmin TO molly;
GRANT ROLE auditor TO bruce;
GRANT ALL ON TABLE tickit_category_redshift TO PUBLIC;
GRANT ALL ON TABLE tickit_sales_redshift TO PUBLIC;
GRANT ALL ON TABLE tickit_event_redshift TO PUBLIC;

-- Create table and schema referenced in the policy statements.
CREATE SCHEMA target_schema;
GRANT ALL ON SCHEMA target_schema TO PUBLIC;
CREATE TABLE target_schema.target_event_table (LIKE tickit_event_redshift);
GRANT ALL ON TABLE target_schema.target_event_table TO PUBLIC;

-- Change session to analyst alice.
SET SESSION AUTHORIZATION alice;

-- Check the tuples visible to analyst alice.
-- Should contain all 3 categories.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Change session to security administrator bob.
SET SESSION AUTHORIZATION bob;

CREATE RLS POLICY policy_concerts
WITH (catgroup VARCHAR(10))
USING (catgroup = 'Concerts');

SELECT poldb, polname, polalias, polatts, polqual, polenabled, polmodifiedby FROM svv_rls_policy WHERE poldb = CURRENT_DATABASE();

ATTACH RLS POLICY policy_concerts ON tickit_category_redshift TO ROLE analyst, ROLE dbadmin;

ALTER TABLE tickit_category_redshift ROW LEVEL SECURITY ON;

SELECT * FROM svv_rls_attached_policy;

-- Change session to analyst alice.
SET SESSION AUTHORIZATION alice;

-- Check that tuples with only `Concert` category will be visible to analyst alice.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Change session to consumer joe.
SET SESSION AUTHORIZATION joe;

-- Although the policy is attached to a different role, no tuples will be
-- visible to consumer joe because the default deny all policy is applied.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Change session to dbadmin molly.
SET SESSION AUTHORIZATION molly;

-- Check that tuples with only `Concert` category will be visible to dbadmin molly.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Check that EXPLAIN output contains RLS SecureScan to prevent disclosure of
-- sensitive information such as RLS filters.
EXPLAIN SELECT catgroup, count(*) FROM tickit_category_redshift GROUP BY catgroup ORDER BY catgroup;

-- Change session to security administrator bob.
SET SESSION AUTHORIZATION bob;

-- Grant IGNORE RLS privilege so that RLS policies do not get applicable to role dbadmin.
GRANT IGNORE RLS TO ROLE dbadmin;

-- Grant EXPLAIN RLS privilege so that anyone in role auditor can view complete EXPLAIN output.
GRANT EXPLAIN RLS TO ROLE auditor;

-- Change session to dbadmin molly.
SET SESSION AUTHORIZATION molly;

-- Check that all tuples are visible to dbadmin molly because `IGNORE RLS` is granted to role dbadmin.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Change session to auditor bruce.
SET SESSION AUTHORIZATION bruce;

-- Check explain plan is visible to auditor bruce because `EXPLAIN RLS` is granted to role auditor.
EXPLAIN SELECT catgroup, count(*) FROM tickit_category_redshift GROUP BY catgroup ORDER BY catgroup;

-- Change session to security administrator bob.
SET SESSION AUTHORIZATION bob;

DETACH RLS POLICY policy_concerts ON tickit_category_redshift FROM ROLE analyst, ROLE dbadmin;

-- Change session to analyst alice.
SET SESSION AUTHORIZATION alice;

-- Check that no tuples are visible to analyst alice.
-- Although the policy is detached, no tuples will be visible to analyst alice
-- because of default deny all policy is applied if the table has RLS on.
SELECT catgroup, count(*)
FROM tickit_category_redshift
GROUP BY catgroup ORDER BY catgroup;

-- Change session to security administrator bob.
SET SESSION AUTHORIZATION bob;

CREATE RLS POLICY policy_events
WITH (eventid INTEGER) AS ev
USING (
    ev.eventid IN (SELECT eventid FROM tickit_sales_redshift WHERE qtysold <3)
);

ATTACH RLS POLICY policy_events ON tickit_event_redshift TO ROLE analyst;
ATTACH RLS POLICY policy_events ON target_schema.target_event_table TO ROLE consumer;

RESET SESSION AUTHORIZATION;

-- Can not cannot alter type of dependent column.
ALTER TABLE target_schema.target_event_table ALTER COLUMN eventid TYPE float;
ALTER TABLE tickit_event_redshift ALTER COLUMN eventid TYPE float;
ALTER TABLE tickit_sales_redshift ALTER COLUMN eventid TYPE float;
ALTER TABLE tickit_sales_redshift ALTER COLUMN qtysold TYPE float;

-- Can not cannot rename dependent column.
ALTER TABLE target_schema.target_event_table RENAME COLUMN eventid TO renamed_eventid;
ALTER TABLE tickit_event_redshift RENAME COLUMN eventid TO renamed_eventid;
ALTER TABLE tickit_sales_redshift RENAME COLUMN eventid TO renamed_eventid;
ALTER TABLE tickit_sales_redshift RENAME COLUMN qtysold TO renamed_qtysold;

-- Can not drop dependent column.
ALTER TABLE target_schema.target_event_table DROP COLUMN eventid CASCADE;
ALTER TABLE tickit_event_redshift DROP COLUMN eventid CASCADE;
ALTER TABLE tickit_sales_redshift DROP COLUMN eventid CASCADE;
ALTER TABLE tickit_sales_redshift DROP COLUMN qtysold CASCADE;

-- Can not drop lookup table.
DROP TABLE tickit_sales_redshift CASCADE;

-- Change session to security administrator bob.
SET SESSION AUTHORIZATION bob;

DROP RLS POLICY policy_concerts;
DROP RLS POLICY IF EXISTS policy_events;

ALTER TABLE tickit_category_redshift ROW LEVEL SECURITY OFF;

RESET SESSION AUTHORIZATION;

-- Drop users and roles.
DROP USER bob;
DROP USER alice;
DROP USER joe;
DROP USER molly;
DROP USER bruce;
DROP ROLE analyst;
DROP ROLE consumer;
DROP ROLE auditor FORCE;
DROP ROLE dbadmin FORCE;
```