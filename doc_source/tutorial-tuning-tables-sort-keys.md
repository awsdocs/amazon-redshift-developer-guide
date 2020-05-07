# Step 3: Select sort keys<a name="tutorial-tuning-tables-sort-keys"></a>

When you create a table, you can specify one or more columns as the sort key\. Amazon Redshift stores your data on disk in sorted order according to the sort key\. How your data is sorted has an important effect on disk I/O, columnar compression, and query performance\. 

In this step, you choose sort keys for the SSB tables based on these best practices: 
+ If recent data is queried most frequently, specify the timestamp column as the leading column for the sort key\. 
+ If you do frequent range filtering or equality filtering on one column, specify that column as the sort key\. 
+ If you frequently join a \(dimension\) table, specify the join column as the sort key\. 

## To select sort keys<a name="tutorial-tuning-tables-to-select-sort-keys"></a>

1. Evaluate your queries to find timestamp columns that are used to filter the results\. 

   For example, LINEORDER frequently uses equality filters using `lo_orderdate`\. 

   ```
   where lo_orderdate = d_datekey and d_year = 1997
   ```

1. Look for columns that are used in range filters and equality filters\. For example, LINEORDER also uses `lo_orderdate` for range filtering\. 

   ```
   where lo_orderdate = d_datekey and d_year >= 1992 and d_year <= 1997 
   ```

1. Based on the first two best practices, `lo_orderdate` is a good choice for sort key\. 

   In the tuning table, specify `lo_orderdate` as the sort key for LINEORDER\. 

1. The remaining tables are dimensions, so, based on the third best practice, specify their primary keys as sort keys\. 

The following tuning table shows the chosen sort keys\. You fill in the Distribution Style column in [Step 4: Select distribution styles](tutorial-tuning-tables-distribution.md)\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-sort-keys.html)

## Next step<a name="next-step-distribution"></a>

[Step 4: Select distribution styles](tutorial-tuning-tables-distribution.md)