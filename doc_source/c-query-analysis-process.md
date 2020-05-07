# Query analysis workflow<a name="c-query-analysis-process"></a>

If a query is taking longer than expected, use the following steps to identify and correct issues that might be negatively affecting the query’s performance\. If you aren’t sure what queries in your system might benefit from performance tuning, start by running the diagnostic query in [Identifying queries that are top candidates for tuning](diagnostic-queries-for-query-tuning.md#identify-queries-that-are-top-candidates-for-tuning)\.

1. Make sure your tables are designed according to best practices\. For more information, see [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md)\.

1. See if you can delete or archive any unneeded data in your tables\. For example, suppose your queries always target the last 6 months’ worth of data but you have the last 18 months’ worth in your tables\. In this case, you can delete or archive the older data to reduce the number of records that need to be scanned and distributed\.

1. Run the [VACUUM](r_VACUUM_command.md) command on the tables in the query to reclaim space and re\-sort rows\. Running VACUUM helps if the unsorted region is large and the query uses the sort key in a join or in the predicate\.

1. Run the [ANALYZE](r_ANALYZE.md) command on the tables in the query to make sure statistics are up to date\. Running ANALYZE helps if any of the tables in the query have recently changed a lot in size\. If running a full ANALYZE command will take too long, run ANALYZE on a single column to reduce processing time\. This approach still updates the table size statistics; table size is a significant factor in query planning\.

1. Make sure that your query has been run once for each type of client \(based on what type of connection protocol the client uses\) so that the query is compiled and cached\. This approach speeds up subsequent runs of the query\. For more information, see [Factors affecting query performance](c-query-performance.md)\.

1. Check the [STL\_ALERT\_EVENT\_LOG](r_STL_ALERT_EVENT_LOG.md) table to identify and correct possible issues with your query\. For more information, see [Reviewing query alerts](c-reviewing-query-alerts.md)\.

1. Run the [EXPLAIN](r_EXPLAIN.md) command to get the query plan and use it to optimize the query\. For more information, see [Analyzing the query plan](c-analyzing-the-query-plan.md)\.

1. Use the [SVL\_QUERY\_SUMMARY](r_SVL_QUERY_SUMMARY.md) and [SVL\_QUERY\_REPORT](r_SVL_QUERY_REPORT.md) views to get summary information and use it to optimize the query\. For more information, see [Analyzing the query summary](c-analyzing-the-query-summary.md)\.

Sometimes a query that should execute quickly is forced to wait until another, longer\-running query finishes\. In that case, you might have nothing to improve in the query itself, but you can improve overall system performance by creating and using query queues for different types of queries\. To get an idea of queue wait time for your queries, see [Reviewing queue wait times for queries](diagnostic-queries-for-query-tuning.md#review-queue-wait-times-for-queries)\. For more information about configuring query queues, see [Implementing workload management](cm-c-implementing-workload-management.md)\.