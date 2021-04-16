# Performing a merge operation by specifying a column list<a name="merge-specify-a-column-list"></a>

**To perform a merge operation by specifying a column list**

1. Put the entire operation in a single transaction block so that if there is a problem, everything will be rolled back\. 

   ```
   begin transaction;
   … 
   end transaction;
   ```

1. Create a staging table, and then populate it with data to be merged, as shown in the following pseudocode\. 

   ```
   create temp table stage (like target); 
   insert into stage 
   select * from source 
   where source.filter = 'filter_expression';
   ```

1. Update the target table by using an inner join with the staging table\. 
   + In the UPDATE clause, explicitly list the columns to be updated\. 
   + Perform an inner join with the staging table\. 
   + If the distribution key is different from the primary key and the distribution key is not being updated, add a redundant join on the distribution key\. To verify that the query will use a collocated join, run the query with [EXPLAIN](r_EXPLAIN.md) and check for DS\_DIST\_NONE on all of the joins\. For more information, see [Evaluating the query plan](c_data_redistribution.md)
   + If your target table is sorted by timestamp, add a predicate to take advantage of range\-restricted scans on the target table\. For more information, see [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md)\.
   + If you will not use all of the rows in the merge, add a clause to filter the rows that need to be changed\. For example, add an inequality filter on one or more columns to exclude rows that have not changed\.
   + Put the update, delete, and insert operations in a single transaction block so that if there is a problem, everything will be rolled back\.

    For example: 

   ```
   begin transaction;
   
   update target 
   set col1 = stage.col1, 
   col2 = stage.col2, 
   col3 = 'expression' 
   from stage 
   where target.primarykey = stage.primarykey 
   and target.distkey = stage.distkey 
   and target.col3 > 'last_update_time' 
   and (target.col1 != stage.col1 
   or target.col2 != stage.col2 
   or target.col3 = 'filter_expression');
   ```

1. Delete unneeded rows from the staging table by using an inner join with the target table\. Some rows in the target table already match the corresponding rows in the staging table, and others were updated in the previous step\. In either case, they are not needed for the insert\. 

   ```
   delete from stage 
   using target 
   where stage.primarykey = target.primarykey;
   ```

1. Insert the remaining rows from the staging table\. Use the same column list in the VALUES clause that you used in the UPDATE statement in step two\. 

   ```
   insert into target
   (select col1, col2, 'expression'
   from stage);
   
   end transaction;
   ```

1. Drop the staging table\. 

   ```
   drop table stage;
   ```