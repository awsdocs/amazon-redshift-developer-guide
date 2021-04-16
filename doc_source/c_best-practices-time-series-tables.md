# Use time\-series tables<a name="c_best-practices-time-series-tables"></a>

If your data has a fixed retention period, you can organize your data as a sequence of time\-series tables\. In such a sequence, each table is identical but contains data for different time ranges\.

You can easily remove old data simply by running a DROP TABLE command on the corresponding tables\. This approach is much faster than running a large\-scale DELETE process and saves you from having to run a subsequent VACUUM process to reclaim space\. To hide the fact that the data is stored in different tables, you can create a UNION ALL view\. When you delete old data, simply refine your UNION ALL view to remove the dropped tables\. Similarly, as you load new time periods into new tables, add the new tables to the view\. To signal the optimizer to skip the scan on tables that don't match the query filter, your view definition filters for the date range that corresponds to each table\.

Avoid having too many tables in the UNION ALL view\. Each additional table adds a small processing time to the query\. Tables don't need to use the same time frame\. For example, you might have tables for differing time periods, such as daily, monthly, and yearly\.

If you use time\-series tables with a timestamp column for the sort key, you effectively load your data in sort key order\. Doing this eliminates the need to vacuum to re\-sort the data\. For more information, see [Loading your data in sort key order](vacuum-load-in-sort-key-order.md)\.