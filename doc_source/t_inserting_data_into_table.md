# Insert Data Rows into a Table<a name="t_inserting_data_into_table"></a>

After you create a table, you can insert rows of data into that table\.

**Note**  
The [INSERT](r_INSERT_30.md) command inserts individual rows into a database table\. For standard bulk loads, use the [COPY](r_COPY.md) command\. For more information, see [Use a COPY Command to Load Data](c_best-practices-use-copy.md)\.

For example, to insert a value of `100` into the `testtable` table \(which contains a single column\), issue the following command:

```
insert into testtable values (100);
```