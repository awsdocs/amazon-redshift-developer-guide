# Amazon Redshift best practices for loading data<a name="c_loading-data-best-practices"></a>

**Topics**
+ [Take the loading data tutorial](c_best-practices-loading-take-loading-data-tutorial.md)
+ [Take the tuning table design tutorial](c_best-practices-loading-take-table-design-tutorial.md)
+ [Use a COPY command to load data](c_best-practices-use-copy.md)
+ [Use a single COPY command to load from multiple files](c_best-practices-single-copy-command.md)
+ [Split your load data into multiple files](c_best-practices-use-multiple-files.md)
+ [Compress your data files](c_best-practices-compress-data-files.md)
+ [Use a manifest file](best-practices-preventing-load-data-errors.md)
+ [Verify data files before and after a load](c_best-practices-verifying-data-files.md)
+ [Use a multi\-row insert](c_best-practices-multi-row-inserts.md)
+ [Use a bulk insert](c_best-practices-bulk-inserts.md)
+ [Load data in sort key order](c_best-practices-sort-key-order.md)
+ [Load data in sequential blocks](c_best-practices-load-data-in-sequential-blocks.md)
+ [Use time\-series tables](c_best-practices-time-series-tables.md)
+ [Use a staging table to perform a merge \(upsert\)](c_best-practices-upsert.md)
+ [Schedule around maintenance windows](c_best-practices-avoid-maintenance.md)

Loading very large datasets can take a long time and consume a lot of computing resources\. How your data is loaded can also affect query performance\. This section presents best practices for loading data efficiently using COPY commands, bulk inserts, and staging tables\. 