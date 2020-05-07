# Using time series tables<a name="vacuum-time-series-tables"></a>

If you maintain data for a rolling time period, use a series of tables, as the following diagram illustrates\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/vacuum-example-unsorted-region-copy-time-series.png)

Create a new table each time you add a set of data, then delete the oldest table in the series\. You gain a double benefit: 
+ You avoid the added cost of deleting rows, because a DROP TABLE operation is much more efficient than a mass DELETE\.
+ If the tables are sorted by timestamp, no vacuum is needed\. If each table contains data for one month, a vacuum will at most have to rewrite one month’s worth of data, even if the tables are not sorted by timestamp\.

You can create a UNION ALL view for use by reporting queries that hides the fact that the data is stored in multiple tables\. If a query filters on the sort key, the query planner can efficiently skip all the tables that aren't used\. A UNION ALL can be less efficient for other types of queries, so you should evaluate query performance in the context of all queries that use the tables\.