# Step 3: Create a database table<a name="t_creating_table"></a>

After you create your new database, you create tables to hold your database data\. You specify any column information for the table when you create the table\.

For example, to create a table named **testtable** with a single column named **testcol** for an integer data type, issue the following command:

```
create table testtable (testcol int);
```

The PG\_TABLE\_DEF system table contains information about all the tables in the cluster\. To verify the result, issue the following SELECT command to query the PG\_TABLE\_DEF system table\. 

```
 select * from pg_table_def where tablename = 'testtable';
```

The query result should look something like this: 

```
schemaname|tablename|column | type  |encoding|distkey|sortkey | notnull
----------+---------+-------+-------+--------+-------+--------+---------
 public   |testtable|testcol|integer|none    |f      |      0 | f
(1 row)
```

By default, new database objects, such as tables, are created in a schema named "public"\. For more information about schemas, see [Schemas](r_Schemas_and_tables.md) in the Managing Database Security section\.

The `encoding`, `distkey`, and `sortkey` columns are used by Amazon Redshift for parallel processing\. For more information about designing tables that incorporate these elements, see [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md)\.