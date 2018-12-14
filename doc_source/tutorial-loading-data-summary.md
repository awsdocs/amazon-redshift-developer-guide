# Summary<a name="tutorial-loading-data-summary"></a>

In this tutorial, you uploaded data files to Amazon S3 and then used COPY commands to load the data from the files into Amazon Redshift tables\.

You loaded data using the following formats:
+ Character\-delimited
+ CSV
+ Fixed\-width

You used the STL\_LOAD\_ERRORS system table to troubleshoot load errors, and then used the REGION, MANIFEST, MAXERROR, ACCEPTINVCHARS, DATEFORMAT, and NULL AS options to resolve the errors\.

You applied the following best practices for loading data: 
+ [Use a COPY Command to Load Data](c_best-practices-use-copy.md)
+ [Split Your Load Data into Multiple Files](c_best-practices-use-multiple-files.md)
+ [Use a Single COPY Command to Load from Multiple Files](c_best-practices-single-copy-command.md)
+ [Compress Your Data Files](c_best-practices-compress-data-files.md)
+ [Use a Manifest File](best-practices-preventing-load-data-errors.md)
+ [Verify Data Files Before and After a Load](c_best-practices-verifying-data-files.md)

For more information about Amazon Redshift best practices, see the following links: 
+ [Amazon Redshift Best Practices for Loading Data](c_loading-data-best-practices.md)
+ [Amazon Redshift Best Practices for Designing Tables](c_designing-tables-best-practices.md) 
+ [Amazon Redshift Best Practices for Designing Queries](c_designing-queries-best-practices.md) 

## Next Step<a name="tutorial-loading-next-step-tuning-tutorial"></a>

For your next step, if you haven't done so already, we recommend taking [Tutorial: Tuning Table Design](tutorial-tuning-tables.md)\. 