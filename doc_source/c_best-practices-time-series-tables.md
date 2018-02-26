# Use Time\-Series Tables<a name="c_best-practices-time-series-tables"></a>

If your data has a fixed retention period, we strongly recommend that you organize your data as a sequence of time\-series tables, where each table is identical but contains data for different time ranges\.

You can easily remove old data simply by executing a DROP TABLE on the corresponding tables, which is much faster than running a large scale DELETE, and also saves you from having to run a subsequent VACUUM to reclaim space\. You can create a UNION ALL view to hide the fact that the data is stored in different tables\. When you delete old data, simply refine your UNION ALL view to remove the dropped tables\. Similarly, as you load new time periods into new tables, add the new tables to the view\.

If you use time\-series tables with a timestamp column for the sort key, you effectively load your data in sort key order, which eliminates the need to vacuum to resort the data\. For more information, see [Load Data in Sort Key Order](c_best-practices-sort-key-order.md)\.