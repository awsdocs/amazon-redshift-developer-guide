# Use a bulk insert<a name="c_best-practices-bulk-inserts"></a>

Use a bulk insert operation with a SELECT clause for high\-performance data insertion\.

Use the [INSERT](r_INSERT_30.md) and [CREATE TABLE AS](r_CREATE_TABLE_AS.md) commands when you need to move data or a subset of data from one table into another\.

For example, the following INSERT statement selects all of the rows from the CATEGORY table and inserts them into the CATEGORY\_STAGE table\.

```
insert into category_stage
(select * from category);
```

The following example creates CATEGORY\_STAGE as a copy of CATEGORY and inserts all of the rows in CATEGORY into CATEGORY\_STAGE\. 

```
create table category_stage as
select * from category;
```