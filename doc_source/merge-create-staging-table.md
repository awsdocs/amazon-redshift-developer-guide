# Creating a temporary staging table<a name="merge-create-staging-table"></a>

The *staging table* is a temporary table that holds all of the data that will be used to make changes to the *target table*, including both updates and inserts\. 

A merge operation requires a join between the staging table and the target table\. To collocate the joining rows, set the staging table's distribution key to the same column as the target table's distribution key\. For example, if the target table uses a foreign key column as its distribution key, use the same column for the staging table's distribution key\. If you create the staging table by using a [CREATE TABLE LIKE](r_CREATE_TABLE_NEW.md#create-table-like) statement, the staging table will inherit the distribution key from the parent table\. If you use a CREATE TABLE AS statement, the new table does not inherit the distribution key\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)

If the distribution key is not the same as the primary key and the distribution key is not updated as part of the merge operation, add a redundant join predicate on the distribution key columns to enable a collocated join\. For example: 

```
where target.primarykey = stage.primarykey 
and target.distkey = stage.distkey
```

To verify that the query will use a collocated join, run the query with [EXPLAIN](r_EXPLAIN.md) and check for DS\_DIST\_NONE on all of the joins\. For more information, see [Evaluating the query plan](c_data_redistribution.md)