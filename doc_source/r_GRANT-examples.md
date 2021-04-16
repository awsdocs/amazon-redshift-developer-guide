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

## Examples of granting the USAGE privilege for data shares<a name="r_GRANT-examples-datashare"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

The following example grants the USAGE privilege on the `Salesshare` data share to the specified namespace\. 

```
GRANT USAGE ON DATASHARE Salesshare TO NAMESPACE 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b';
```

The following example grants the USAGE privilege on the `Sales_db` to Bob\.

```
GRANT USAGE ON DATABASE Sales_db TO Bob;
```

The following example GRANT USAGE privilege on the `Sales_schema` to the `Analyst_group`\.

```
GRANT USAGE ON SCHEMA Sales_schema TO GROUP Analyst_group;
```

## Examples of granting the ASSUMEROLE privilege<a name="r_GRANT-examples-assumerole"></a>

The following are examples of granting the ASSUMEROLE privilege\. 

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