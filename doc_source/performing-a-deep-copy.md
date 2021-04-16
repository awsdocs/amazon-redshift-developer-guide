# Performing a deep copy<a name="performing-a-deep-copy"></a>

A deep copy recreates and repopulates a table by using a bulk insert, which automatically sorts the table\. If a table has a large unsorted Region, a deep copy is much faster than a vacuum\. The trade off is that you should not make concurrent updates during a deep copy operation unless you can track it and move the delta updates into the new table after the process has completed\. A VACUUM operation supports concurrent updates automatically\.

You can choose one of the following methods to create a copy of the original table: 
+ Use the original table DDL\. 

  If the CREATE TABLE DDL is available, this is the fastest and preferred method\. If you create a new table, you can specify all table and column attributes, including primary key and foreign keys\.
**Note**  
If the original DDL is not available, you might be able to recreate the DDL by running a script called `v_generate_tbl_ddl`\. You can download the script from [amazon\-redshift\-utils](https://github.com/awslabs/amazon-redshift-utils/blob/master/src/AdminViews/v_generate_tbl_ddl.sql), which is part of the [Amazon Web Services \- Labs](https://github.com/awslabs) git hub repository\.
+ Use CREATE TABLE LIKE\. 

  If the original DDL is not available, you can use CREATE TABLE LIKE to recreate the original table\. The new table inherits the encoding, distkey, sortkey, and notnull attributes of the parent table\. The new table doesn't inherit the primary key and foreign key attributes of the parent table, but you can add them using [ALTER TABLE](r_ALTER_TABLE.md)\.
+ Create a temporary table and truncate the original table\. 

  If you need to retain the primary key and foreign key attributes of the parent table, or if the parent table has dependencies, you can use CREATE TABLE \.\.\. AS \(CTAS\) to create a temporary table, then truncate the original table and populate it from the temporary table\. 

  Using a temporary table improves performance significantly compared to using a permanent table, but there is a risk of losing data\. A temporary table is automatically dropped at the end of the session in which it is created\. TRUNCATE commits immediately, even if it is inside a transaction block\. If the TRUNCATE succeeds but the session terminates before the subsequent INSERT completes, the data is lost\. If data loss is unacceptable, use a permanent table\. 

**To perform a deep copy using the original table DDL**

1. \(Optional\) Recreate the table DDL by running a script called `v_generate_tbl_ddl`\. 

1. Create a copy of the table using the original CREATE TABLE DDL\.

1. Use an INSERT INTO … SELECT statement to populate the copy with data from the original table\. 

1. Drop the original table\.

1. Use an ALTER TABLE statement to rename the copy to the original table name\.

The following example performs a deep copy on the SALES table using a duplicate of SALES named SALESCOPY\.

```
create table salescopy ( … );
insert into salescopy (select * from sales);
drop table sales;
alter table salescopy rename to sales;
```

**To perform a deep copy using CREATE TABLE LIKE**

1. Create a new table using CREATE TABLE LIKE\. 

1. Use an INSERT INTO … SELECT statement to copy the rows from the current table to the new table\. 

1. Drop the current table\. 

1. Use an ALTER TABLE statement to rename the new table to the original table name\. 

The following example performs a deep copy on the SALES table using CREATE TABLE LIKE\.

```
create table likesales (like sales);
insert into likesales (select * from sales);
drop table sales;
alter table likesales rename to sales;
```

**To perform a deep copy by creating a temporary table and truncating the original table**

1. Use CREATE TABLE AS to create a temporary table with the rows from the original table\. 

1. Truncate the current table\. 

1. Use an INSERT INTO … SELECT statement to copy the rows from the temporary table to the original table\. 

1. Drop the temporary table\. 

The following example performs a deep copy on the SALES table by creating a temporary table and truncating the original table:

```
create temp table salestemp as select * from sales;
truncate sales;
insert into sales (select * from salestemp);
drop table salestemp;
```