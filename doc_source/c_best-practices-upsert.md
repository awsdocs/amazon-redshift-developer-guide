# Use a staging table to perform a merge \(upsert\)<a name="c_best-practices-upsert"></a>

You can efficiently update and insert new data by loading your data into a staging table first\.

Amazon Redshift doesn't support a single *merge* statement \(update or insert, also known as an *upsert*\) to insert and update data from a single data source\. However, you can effectively perform a merge operation\. To do so, load your data into a staging table and then join the staging table with your target table for an UPDATE statement and an INSERT statement\. For instructions, see [Updating and inserting new data](t_updating-inserting-using-staging-tables-.md)\.