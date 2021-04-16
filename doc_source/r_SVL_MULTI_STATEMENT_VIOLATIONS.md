# SVL\_MULTI\_STATEMENT\_VIOLATIONS<a name="r_SVL_MULTI_STATEMENT_VIOLATIONS"></a>

Use the SVL\_MULTI\_STATEMENT\_VIOLATIONS view to get a complete record of all of the SQL commands run on the system that violates transaction block restrictions\.

Violations occur when you run any of the following SQL commands that Amazon Redshift restricts inside a transaction block or multi\-statement requests:
+ [CREATE DATABASE](r_CREATE_DATABASE.md)
+ [DROP DATABASE](r_DROP_DATABASE.md)
+ [ALTER TABLE APPEND](r_ALTER_TABLE_APPEND.md)
+ [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)
+ DROP EXTERNAL TABLE
+ RENAME EXTERNAL TABLE
+ ALTER EXTERNAL TABLE
+ CREATE TABLESPACE
+ DROP TABLESPACE
+ [CREATE LIBRARY](r_CREATE_LIBRARY.md)
+ [DROP LIBRARY](r_DROP_LIBRARY.md)
+ REBUILDCAT
+ INDEXCAT
+ REINDEX DATABASE
+ [VACUUM](r_VACUUM_command.md)
+ [GRANT](r_GRANT.md)
+ [COPY](r_COPY.md)

**Note**  
If there are any entries in this view, then change your corresponding applications and SQL scripts\. We recommend changing your application code to move the use of these restricted SQL commands outside of the transaction block\. If you need further assistance, contact AWS Support\.

SVL\_MULTI\_STATEMENT\_VIOLATIONS is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_MULTI_STATEMENT_VIOLATIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_MULTI_STATEMENT_VIOLATIONS.html)

## Sample query<a name="r_SVL_MULTI_STATEMENT_VIOLATIONS-sample-query"></a>

The following query returns multiple statements that have violations\.

```
select * from svl_multi_statement_violations order by starttime asc;

userid | database | cmdname | xid | pid | label | starttime | endtime | sequence | type | text
==============================================================================================================================
1 | dev | CREATE DATABASE | 1034 | 5729 |label1 | ********* | ******* | 0 | DDL | create table c(b int);
1 | dev | CREATE DATABASE | 1034 | 5729 |label1 | ********* | ******* | 0 | UTILITY | create database b;
1 | dev | CREATE DATABASE | 1034 | 5729 |label1 | ********* | ******* | 0 | UTILITY | COMMIT
...
```