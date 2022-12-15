# Using RLS policies in SQL statements<a name="t_rls_statements"></a>

When using RLS policies in SQL statements, Amazon Redshift applies the following rules:
+ Amazon Redshift applies RLS policies to the SELECT, UPDATE, and DELETE statements by default\. 
+ For SELECT and UNLOAD, Amazon Redshift filters rows according to your defined policy\.
+ For UPDATE, Amazon Redshift updates only the rows that are visible to you\. If a policy restricts a subset of the rows in a table, then you can't update them\.
+ For DELETE, you can delete only the rows that are visible to you\. If a policy restricts a subset of the rows in a table, then you can't delete them\. For TRUNCATE, you can still truncate the table\.
+ For CREATE TABLE LIKE, tables created with the LIKE options won't inherit permission settings from the source table\. Similarly, the target table won't inherit the RLS policies from source table\.