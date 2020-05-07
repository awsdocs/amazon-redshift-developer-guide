# Reviewing query alerts<a name="c-reviewing-query-alerts"></a>

To use the [STL\_ALERT\_EVENT\_LOG](r_STL_ALERT_EVENT_LOG.md) system table to identify and correct potential performance issues with your query, follow these steps:

1. Run the following to determine your query ID:

   ```
   select query, elapsed, substring
   from svl_qlog
   order by query
   desc limit 5;
   ```

   Examine the truncated query text in the `substring` field to determine which `query` value to select\. If you have run the query more than once, use the `query` value from the row with the lower `elapsed` value\. That is the row for the compiled version\. If you have been running many queries, you can raise the value used by the LIMIT clause used to make sure that your query is included\.

1. Select rows from STL\_ALERT\_EVENT\_LOG for your query:

   ```
   Select * from stl_alert_event_log where query = MyQueryID;               
   ```  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/stl_alert_event_log_results.png)

1. Evaluate the results for your query\. Use the following table to locate potential solutions for any issues that you have identified\.
**Note**  
Not all queries have rows in STL\_ALERT\_EVENT\_LOG, only those with identified issues\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c-reviewing-query-alerts.html)