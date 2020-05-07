# Summary<a name="tutorial-loading-data-summary"></a>

In this tutorial, you uploaded data files to Amazon S3 and then used COPY commands to load the data from the files into Amazon Redshift tables\.

You loaded data using the following formats:
+ Character\-delimited
+ CSV
+ Fixed\-width

You used the STL\_LOAD\_ERRORS system table to troubleshoot load errors, and then used the REGION, MANIFEST, MAXERROR, ACCEPTINVCHARS, DATEFORMAT, and NULL AS options to resolve the errors\.

You applied the following best practices for loading data: 
+ [Use a COPY command to load data](c_best-practices-use-copy.md)
+ [Split your load data into multiple files](c_best-practices-use-multiple-files.md)
+ [Use a single COPY command to load from multiple files](c_best-practices-single-copy-command.md)
+ [Compress your data files](c_best-practices-compress-data-files.md)
+ [Use a manifest file](best-practices-preventing-load-data-errors.md)
+ [Verify data files before and after a load](c_best-practices-verifying-data-files.md)

For more information about Amazon Redshift best practices, see the following links: 
+ [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md)
+ [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md) 
+ [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md) 

## Next step<a name="tutorial-loading-next-step-tuning-tutorial"></a>

For your next step, if you haven't done so already, we recommend taking [Tutorial: Tuning table design](tutorial-tuning-tables.md)\. 