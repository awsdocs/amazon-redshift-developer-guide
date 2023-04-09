# Updating and inserting new data<a name="t_updating-inserting-using-staging-tables-"></a>

You can efficiently add new data to an existing table by using a combination of updates and inserts from a staging table\. While Amazon Redshift does not support a single *merge*, or *upsert*, command to update a table from a single data source, you can perform a merge operation by creating a staging table and then using one of the methods described in this section to update the target table from the staging table\. 

**Topics**
+ [Merge method 1: Replacing existing rows](#merge-method-replace-existing-rows)
+ [Merge method 2: Specifying a column list](#merge-method-specify-column-list)
+ [Creating a temporary staging table](merge-create-staging-table.md)
+ [Performing a merge operation by replacing existing rows](merge-replacing-existing-rows.md)
+ [Performing a merge operation by specifying a column list](merge-specify-a-column-list.md)
+ [Merge examples](merge-examples.md)

The [Merge examples](merge-examples.md) use a sample dataset for Amazon Redshift, called the TICKIT data set\. As a prerequisite, you can set up the TICKIT tables and data by following the instructions available in [Getting started with common database tasks](https://docs.aws.amazon.com/redshift/latest/gsg/database-tasks.html)\. More detailed information about the sample data set is found at [Sample database](https://docs.aws.amazon.com/redshift/latest/dg/c_sampledb.html)\. 

## Merge method 1: Replacing existing rows<a name="merge-method-replace-existing-rows"></a>

If you are overwriting all of the columns in the target table, the fastest method to perform a merge is to replace the existing rows\. This scans the target table only once, by using an inner join to delete rows that will be updated\. After the rows are deleted, they are replaced with new rows by a single insert operation from the staging table\. 

Use this method if all of the following are true: 
+ Your target table and your staging table contain the same columns\. 
+ You intend to replace all of the data in the target table columns with all of the staging table columns\.
+ You will use all of the rows in the staging table in the merge\.

If any of these criteria do not apply, use Merge method 2: Specifying a column list, described in the following section\.

If you will not use all of the rows in the staging table, filter the DELETE and INSERT statements by using a WHERE clause to leave out rows that are not changing\. However, if most of the rows in the staging table will not participate in the merge, we recommend performing an UPDATE and an INSERT in separate steps, as described later in this section\.

## Merge method 2: Specifying a column list<a name="merge-method-specify-column-list"></a>

Use this method to update specific columns in the target table instead of overwriting entire rows\. This method takes longer than the previous method because it requires an extra update step\. Use this method if any of the following are true: 
+ Not all of the columns in the target table are to be updated\. 
+ Most rows in the staging table will not be used in the updates\. 