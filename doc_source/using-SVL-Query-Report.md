# Using the SVL\_QUERY\_REPORT view<a name="using-SVL-Query-Report"></a>

To analyze query summary information by slice, do the following:

1. Run the following to determine your query ID:

   ```
   select query, elapsed, substring
   from svl_qlog
   order by query
   desc limit 5;
   ```

   Examine the truncated query text in the `substring` field to determine which `query` value represents your query\. If you have run the query more than once, use the `query` value from the row with the lower `elapsed` value\. That is the row for the compiled version\. If you have been running many queries, you can raise the value used by the LIMIT clause used to make sure that your query is included\.

1. Select rows from SVL\_QUERY\_REPORT for your query\. Order the results by segment, step, elapsed\_time, and rows:

   ```
   select * from svl_query_report where query = MyQueryID order by segment, step, elapsed_time, rows;
   ```

1. For each step, check to see that all slices are processing approximately the same number of rows:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/SVL_QUERY_REPORT_rows.png)

   Also check to see that all slices are taking approximately the same amount of time:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/SVL_QUERY_REPORT_elapsed_time.png)

   Large discrepancies in these values can indicate data distribution skew due to a suboptimal distribution style for this particular query\. For recommended solutions, see [Suboptimal data distribution](query-performance-improvement-opportunities.md#suboptimal-data-distribution)\.