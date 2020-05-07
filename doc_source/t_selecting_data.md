# Select data from a table<a name="t_selecting_data"></a>

After you create a table and populate it with data, use a SELECT statement to display the data contained in the table\. The SELECT \* statement returns all the column names and row values for all of the data in a table and is a good way to verify that recently added data was correctly inserted into the table\.

To view the data that you entered in the **testtable** table, issue the following command:

```
select * from testtable;
```

The result will look like this:

```
 testcol
---------
100
(1 row)
```

For more information about using the SELECT statement to query tables, see [SELECT](r_SELECT_synopsis.md) in the SQL Command Reference\.