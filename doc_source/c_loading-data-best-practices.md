# Amazon Redshift Best Practices for Loading Data<a name="c_loading-data-best-practices"></a>

**Topics**
+ [Take the Loading Data Tutorial](c_best-practices-loading-take-loading-data-tutorial.md)
+ [Take the Tuning Table Design Tutorial](c_best-practices-loading-take-table-design-tutorial.md)
+ [Use a COPY Command to Load Data](c_best-practices-use-copy.md)
+ [Use a Single COPY Command to Load from Multiple Files](c_best-practices-single-copy-command.md)
+ [Split Your Load Data into Multiple Files](c_best-practices-use-multiple-files.md)
+ [Compress Your Data Files](c_best-practices-compress-data-files.md)
+ [Use a Manifest File](best-practices-preventing-load-data-errors.md)
+ [Verify Data Files Before and After a Load](c_best-practices-verifying-data-files.md)
+ [Use a Multi\-Row Insert](c_best-practices-multi-row-inserts.md)
+ [Use a Bulk Insert](c_best-practices-bulk-inserts.md)
+ [Load Data in Sort Key Order](c_best-practices-sort-key-order.md)
+ [Load Data in Sequential Blocks](c_best-practices-load-data-in-sequential-blocks.md)
+ [Use Time\-Series Tables](c_best-practices-time-series-tables.md)
+ [Use a Staging Table to Perform a Merge \(Upsert\)](c_best-practices-upsert.md)
+ [Schedule Around Maintenance Windows](c_best-practices-avoid-maintenance.md)

Loading very large datasets can take a long time and consume a lot of computing resources\. How your data is loaded can also affect query performance\. This section presents best practices for loading data efficiently using COPY commands, bulk inserts, and staging tables\. 