# Performing a deep copy<a name="performing-a-deep-copy"></a>

A deep copy recreates and repopulates a table by using a bulk insert, which automatically sorts the table\. If a table has a large unsorted Region, a deep copy is much faster than a vacuum\. We recommend that you only make concurrent updates during a deep copy operation if you can track them\. After the process has completed, move the delta updates into the new table\. A VACUUM operation supports concurrent updates automatically\. 

You can choose one of the following methods to create a copy of the original table: 
+ Use the original table DDL\. 

  If the CREATE TABLE DDL is available, this is the fastest and preferred method\. If you create a new table, you can specify all table and column attributes, including primary key and foreign keys\.
**Note**  
If the original DDL is not available, you might be able to recreate the DDL by running a script called `v_generate_tbl_ddl`\. You can download the script from [amazon\-redshift\-utils](https://github.com/awslabs/amazon-redshift-utils/blob/master/src/AdminViews/v_generate_tbl_ddl.sql), which is part of the [Amazon Web Services \- Labs](https://github.com/awslabs) GitHub repository\.
+ Use CREATE TABLE LIKE\. 

  If the original DDL is not available, you can use CREATE TABLE LIKE to recreate the original table\. The new table inherits the encoding, distribution key, sort key, and not\-null attributes of the parent table\. The new table doesn't inherit the primary key and foreign key attributes of the parent table, but you can add them using [ALTER TABLE](r_ALTER_TABLE.md)\.
+ Create a temporary table and truncate the original table\. 

  If you must retain the primary key and foreign key attributes of the parent table\. If the parent table has dependencies, you can use CREATE TABLE \.\.\. AS \(CTAS\) to create a temporary table\. Then truncate the original table and populate it from the temporary table\. 

  Using a temporary table improves performance significantly compared to using a permanent table, but there is a risk of losing data\. A temporary table is automatically dropped at the end of the session in which it is created\. TRUNCATE commits immediately, even if it is inside a transaction block\. If the TRUNCATE succeeds but the session shuts down before the following INSERT completes, the data is lost\. If data loss is unacceptable, use a permanent table\. 

After you create a copy of a table, you might have to grant access to the new table\. You can use [GRANT](r_GRANT.md) to define access privileges\. To view and grant all of a table's access privileges, you must be one of the following: 
+  A superuser\. 
+  The owner of the table you want to copy\. 
+  A user with the ACCESS SYSTEM TABLE privilege to see the table's privileges, and with the grant privilege for all relevant permissions\. 

Additionally, you might have to grant usage permission for the schema your deep copy is in\. Granting usage permission is necessary if your deep copy's schema is different from the original table's schema, and also isn't the `public` schema\. To view and grant usage privileges you must be one of the following:
+  A superuser\. 
+  A user who can grant the USAGE permission for the deep copy's schema\. 

**To perform a deep copy using the original table DDL**

1. \(Optional\) Recreate the table DDL by running a script called `v_generate_tbl_ddl`\. 

1. Create a copy of the table using the original CREATE TABLE DDL\.

1. Use an INSERT INTO … SELECT statement to populate the copy with data from the original table\. 

1. Check for permissions granted on the old table\. You can see these permissions in the SVV\_RELATION\_PRIVILEGES system view\.

1. If necessary, grant the permissions of the old table to the new table\.

1. Grant usage permission to every group and user that has privileges in the original table\. This step isn't necessary if your deep copy table is in the `public` schema, or is in the same schema as the original table\.

1. Drop the original table\.

1. Use an ALTER TABLE statement to rename the copy to the original table name\.

The following example performs a deep copy on the SAMPLE table using a duplicate of SAMPLE named sample\_copy\.

```
--Create a copy of the original table in the sample_namespace namespace using the original CREATE TABLE DDL.
create table sample_namespace.sample_copy ( … );

--Populate the copy with data from the original table in the public namespace.
insert into sample_namespace.sample_copy (select * from public.sample);

--Check SVV_RELATION_PRIVILEGES for the original table's privileges.
select * from svv_relation_privileges where namespace_name = 'public' and relation_name = 'sample' order by identity_type, identity_id, privilege_type;

--Grant the original table's privileges to the copy table.
grant DELETE on table sample_namespace.sample_copy to group group1;
grant INSERT, UPDATE on table sample_namespace.sample_copy to group group2;
grant SELECT on table sample_namespace.sample_copy to user1;
grant INSERT, SELECT, UPDATE on table sample_namespace.sample_copy to user2;
         
--Grant usage permission to every group and user that has privileges in the original table.
grant USAGE on schema sample_namespace to group group1, group group2, user1, user2;

--Drop the original table.
drop table public.sample;

--Rename the copy table to match the original table's name.
alter table sample_namespace.sample_copy rename to sample;
```

**To perform a deep copy using CREATE TABLE LIKE**

1. Create a new table using CREATE TABLE LIKE\. 

1. Use an INSERT INTO … SELECT statement to copy the rows from the current table to the new table\. 

1. Check for permissions granted on the old table\. You can see these permissions in the SVV\_RELATION\_PRIVILEGES system view\.

1. If necessary, grant the permissions of the old table to the new table\.

1. Grant usage permission to every group and user that has privileges in the original table\. This step isn't necessary if your deep copy table is in the `public` schema, or is in the same schema as the original table\.

1. Drop the current table\. 

1. Use an ALTER TABLE statement to rename the new table to the original table name\. 

The following example performs a deep copy on the SAMPLE table using CREATE TABLE LIKE\.

```
--Create a copy of the original table in the sample_namespace namespace using CREATE TABLE LIKE.
create table sameple_namespace.sample_copy (like public.sample);

--Populate the copy with data from the original table.
insert into sample_namespace.sample_copy (select * from public.sample);

--Check SVV_RELATION_PRIVILEGES for the original table's privileges.
select * from svv_relation_privileges where namespace_name = 'public' and relation_name = 'sample' order by identity_type, identity_id, privilege_type;

--Grant the original table's privileges to the copy table.
grant DELETE on table sample_namespace.sample_copy to group group1;
grant INSERT, UPDATE on table sample_namespace.sample_copy to group group2;
grant SELECT on table sample_namespace.sample_copy to user1;
grant INSERT, SELECT, UPDATE on table sample_namespace.sample_copy to user2;
         
--Grant usage permission to every group and user that has privileges in the original table.
grant USAGE on schema sample_namespace to group group1, group group2, user1, user2;

--Drop the original table.
drop table public.sample;

--Rename the copy table to match the original table's name.
alter table sample_namespace.sample_copy rename to sample;
```

**To perform a deep copy by creating a temporary table and truncating the original table**

1. Use CREATE TABLE AS to create a temporary table with the rows from the original table\. 

1. Truncate the current table\. 

1. Use an INSERT INTO … SELECT statement to copy the rows from the temporary table to the original table\. 

1. Drop the temporary table\. 

The following example performs a deep copy on the SALES table by creating a temporary table and truncating the original table\. Since the original table remains, you don't need to grant permissions to the copy table\.

```
--Create a temp table copy using CREATE TABLE AS.
create temp table salestemp as select * from sales;

--Truncate the original table.
truncate sales;

--Copy the rows from the temporary table to the original table.
insert into sales (select * from salestemp);

--Drop the temporary table.
drop table salestemp;
```