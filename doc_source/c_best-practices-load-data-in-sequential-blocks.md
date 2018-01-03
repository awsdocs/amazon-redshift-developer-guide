# Load Data in Sequential Blocks<a name="c_best-practices-load-data-in-sequential-blocks"></a>

If you need to add a large quantity of data, load the data in sequential blocks according to sort order to eliminate the need to vacuum\. 

For example, suppose you need to load a table with events from January, 2012 to December, 2012\. Load the rows for January, then February, and so on\. Your table will be completely sorted when your load completes and you will not need to run a vacuum\. For more information, see [Use Time\-Series Tables](c_best-practices-time-series-tables.md)\.

When loading very large data sets, the space required to sort might exceed the total available space\. By loading data in smaller blocks, you'll use much less intermediate sort space during each load\. In addition, loading smaller blocks will make it easier to restart if the COPY fails and is rolled back\. 