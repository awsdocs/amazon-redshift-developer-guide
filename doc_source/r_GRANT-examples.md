# Examples<a name="r_GRANT-examples"></a>

 The following example grants the SELECT privilege on the SALES table to the user `fred`\. 

```
grant select on table sales to fred;
```

The following example grants the SELECT privilege on all tables in the QA\_TICKIT schema to the user `fred`\. 

```
grant select on all tables in schema qa_tickit to fred;
```

The following example grants all schema privileges on the schema QA\_TICKIT to the user group QA\_USERS\. Schema privileges are CREATE and USAGE\. USAGE grants users access to the objects in the schema, but doesn't grant privileges such as INSERT or SELECT on those objects\. Grant privileges on each object separately\.

```
create group qa_users;
grant all on schema qa_tickit to group qa_users;
```

The following example grants all privileges on the SALES table in the QA\_TICKIT schema to all users in the group QA\_USERS\. 

```
grant all on table qa_tickit.sales to group qa_users;
```

The following example grants the DROP privilege on the SALES table in the QA\_TICKIT schema to all users in the group QA\_USERS\.

```
grant drop on table qa_tickit.sales to group qa_users;
```

The following sequence of commands shows how access to a schema doesn't grant privileges on a table in the schema\. 

```
create user schema_user in group qa_users password 'Abcd1234';
create schema qa_tickit;
create table qa_tickit.test (col1 int);
grant all on schema qa_tickit to schema_user;

set session authorization schema_user;
select current_user;

current_user
--------------
schema_user
(1 row)

select count(*) from qa_tickit.test;

ERROR: permission denied for relation test [SQL State=42501] 

set session authorization dw_user;
grant select on table qa_tickit.test to schema_user;
set session authorization schema_user;
select count(*) from qa_tickit.test;

count
-------
0
(1 row)
```

The following sequence of commands shows how access to a view doesn't imply access to its underlying tables\. The user called VIEW\_USER can't select from the DATE table, although this user has been granted all privileges on VIEW\_DATE\. 

```
create user view_user password 'Abcd1234';
create view view_date as select * from date;
grant all on view_date to view_user;
set session authorization view_user;
select current_user;

current_user
--------------
view_user
(1 row)

select count(*) from view_date;
count
-------
365
(1 row)

select count(*) from date;
ERROR:  permission denied for relation date
```

The following example grants SELECT privilege on the `cust_name` and `cust_phone` columns of the `cust_profile` table to the user `user1`\. 

```
grant select(cust_name, cust_phone) on cust_profile to user1;
```

The following example grants SELECT privilege on the `cust_name` and `cust_phone` columns and UPDATE privilege on the `cust_contact_preference` column of the `cust_profile` table to the `sales_group` group\. 

```
grant select(cust_name, cust_phone), update(cust_contact_preference) on cust_profile to group sales_group;
```

The following example shows the usage of the ALL keyword to grant both SELECT and UPDATE privileges on three columns of the table `cust_profile` to the `sales_admin` group\. 

```
grant ALL(cust_name, cust_phone,cust_contact_preference) on cust_profile to group sales_admin;
```

The following example grants the SELECT privilege on the `cust_name` column of the `cust_profile_vw` view to the `user2` user\. 

```
grant select(cust_name) on cust_profile_vw to user2;
```

## Examples of granting the USAGE privilege for datashares<a name="r_GRANT-examples-datashare"></a>

The following examples show GRANT datasharing usage permissions on a specific database or schema created from a datashare\. This USAGE permission doesn't grant usage permission to databases that aren't created from the specified datashare\. You can only GRANT or REVOKE ALTER or SHARE permissions on a datashare to users and user groups\.

The following example grants the USAGE privilege on the `salesshare` datashare to the specified namespace\. 

```
GRANT USAGE ON DATASHARE salesshare TO NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
```

The following example grants the USAGE privilege on the `sales_db` to Bob\.

```
GRANT USAGE ON DATABASE sales_db TO Bob;
```

The following example GRANT USAGE privilege on the `sales_schema` to the `Analyst_group`\.

```
GRANT USAGE ON SCHEMA sales_schema TO GROUP Analyst_group;
```

## Examples of granting the ASSUMEROLE privilege<a name="r_GRANT-examples-assumerole"></a>

The following are examples of granting the ASSUMEROLE privilege\.

The following example shows the REVOKE statement that a superuser runs once on the cluster to enable the use of the ASSUMEROLE privilege for users and groups\. Then, the superuser grants the ASSUMEROLE privilege to users and groups for the appropriate commands\. For information on enabling the use of the ASSUMEROLE privilege for users and groups, see [Usage notes for granting the ASSUMEROLE privilege ](r_GRANT-usage-notes.md#r_GRANT-usage-notes-assumerole)\.

```
revoke assumerole on all from public for all;
```

The following example grants the ASSUMEROLE privilege to the user `reg_user1` for the IAM role `Redshift-S3-Read` to perform COPY operations\. 

```
grant assumerole on 'arn:aws:iam::123456789012:role/Redshift-S3-Read' 
to reg_user1 for copy;
```

The following example grants the ASSUMEROLE privilege to the user `reg_user1` for the IAM role chain `RoleA`, `RoleB` to perform UNLOAD operations\. 

```
grant assumerole
on 'arn:aws:iam::123456789012:role/RoleA,arn:aws:iam::210987654321:role/RoleB'
to reg_user1
for unload;
```

The following is an example of the UNLOAD command using the IAM role chain `RoleA`, `RoleB`\.

```
unload ('select * from venue limit 10')
to 's3://companyb/redshift/venue_pipe_'
iam_role 'arn:aws:iam::123456789012:role/RoleA,arn:aws:iam::210987654321:role/RoleB';
```

The following example grants the ASSUMEROLE privilege to the user `reg_user1` for the IAM role `Redshift-Exfunc` to create external functions\. 

```
grant assumerole on 'arn:aws:iam::123456789012:role/Redshift-Exfunc' 
to reg_user1 for external function;
```

The following example grants the ASSUMEROLE privilege to the user `reg_user1` for the IAM role `Redshift-model` to create machine learning models\. 

```
grant assumerole on 'arn:aws:iam::123456789012:role/Redshift-ML' 
to reg_user1 for create model;
```

## Examples of granting the ROLE privileges<a name="r_GRANT-examples-role"></a>

The following example grants sample\_role1 to user1\.

```
CREATE ROLE sample_role1;
GRANT ROLE sample_role1 TO user1;
```

The following example grants sample\_role1 to user1 with the WITH ADMIN OPTION, sets the current session for user1, and user1 grants sample\_role1 to user2\.

```
GRANT ROLE sample_role1 TO user1 WITH ADMIN OPTION;
SET SESSION AUTHORIZATION user1;
GRANT ROLE sample_role1 TO user2;
```

The following example grants sample\_role1 to sample\_role2\.

```
GRANT ROLE sample_role1 TO ROLE sample_role2;
```

The following example grants sample\_role2 to sample\_role3 and sample\_role4\. Then it attempts to grants sample\_role3 to sample\_role1\.

```
GRANT ROLE sample_role2 TO ROLE sample_role3;
GRANT ROLE sample_role3 TO ROLE sample_role2;
ERROR: cannot grant this role, a circular dependency was detected between these roles
```

The following example grants the CREATE USER system privileges to sample\_role1\.

```
GRANT CREATE USER TO ROLE sample_role1;
```

The following example grants the system\-defined role `sys:dba` to user1\.

```
GRANT ROLE sys:dba TO user1;
```

The following example attempts to grant sample\_role3 in a circular dependency to sample\_role2\.

```
CREATE ROLE sample_role3;
GRANT ROLE sample_role2 TO ROLE sample_role3;
GRANT ROLE sample_role3 TO ROLE sample_role2; -- fail
ERROR:  cannot grant this role, a circular dependency was detected between these roles
```