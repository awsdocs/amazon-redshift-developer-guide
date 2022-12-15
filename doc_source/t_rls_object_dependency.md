# Policy\-dependent objects and principles<a name="t_rls_object_dependency"></a>

To provide security for applications and to prevent policy objects from becoming stale or invalid, Amazon Redshift doesn't permit dropping or altering objects referenced by RLS policies\.

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