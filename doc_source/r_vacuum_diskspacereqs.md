# Managing the size of the unsorted region<a name="r_vacuum_diskspacereqs"></a>

The unsorted region grows when you load large amounts of new data into tables that already contain data or when you do not vacuum tables as part of your routine maintenance operations\. To avoid long\-running vacuum operations, use the following practices:
+ Run vacuum operations on a regular schedule\. 

  If you load your tables in small increments \(such as daily updates that represent a small percentage of the total number of rows in the table\), running VACUUM regularly will help ensure that individual vacuum operations go quickly\.
+ Run the largest load first\.

  If you need to load a new table with multiple COPY operations, run the largest load first\. When you run an initial load into a new or truncated table, all of the data is loaded directly into the sorted region, so no vacuum is required\.
+ Truncate a table instead of deleting all of the rows\. 

  Deleting rows from a table does not reclaim the space that the rows occupied until you perform a vacuum operation; however, truncating a table empties the table and reclaims the disk space, so no vacuum is required\. Alternatively, drop the table and re\-create it\. 
+ Truncate or drop test tables\. 

  If you are loading a small number of rows into a table for test purposes, don't delete the rows when you are done\. Instead, truncate the table and reload those rows as part of the subsequent production load operation\. 
+ Perform a deep copy\. 

  If a table that uses a compound sort key table has a large unsorted region, a deep copy is much faster than a vacuum\. A deep copy recreates and repopulates a table by using a bulk insert, which automatically re\-sorts the table\. If a table has a large unsorted region, a deep copy is much faster than a vacuum\. The trade off is that you cannot make concurrent updates during a deep copy operation, which you can do during a vacuum\. For more information, see [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md)\. 