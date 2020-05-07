# Performing a merge operation by replacing existing rows<a name="merge-replacing-existing-rows"></a>

**To perform a merge operation by replacing existing rows**

1. Create a staging table, and then populate it with data to be merged, as shown in the following pseudocode\.

   ```
   create temp table stage (like target); 
   
   insert into stage 
   select * from source 
   where source.filter = 'filter_expression';
   ```

1. Use an inner join with the staging table to delete the rows from the target table that are being updated\. 

   Put the delete and insert operations in a single transaction block so that if there is a problem, everything will be rolled back\. 

   ```
   begin transaction;
   
   delete from target 
   using stage 
   where target.primarykey = stage.primarykey;
   ```

1. Insert all of the rows from the staging table\. 

   ```
   insert into target 
   select * from stage;
   
   end transaction;
   ```

1. Drop the staging table\. 

   ```
   drop table stage;
   ```