# Analyzing the Query Summary<a name="c-analyzing-the-query-summary"></a>

To get execution steps and statistics in more detail than in the query plan than [EXPLAIN](r_EXPLAIN.md) produces, use the [SVL\_QUERY\_SUMMARY](r_SVL_QUERY_SUMMARY.md) and [SVL\_QUERY\_REPORT](r_SVL_QUERY_REPORT.md) system views\.

SVL\_QUERY\_SUMMARY provides query statistics by stream\. You can use the information it provides to identify issues with expensive steps, long\-running steps, and steps that write to disk\. 

The SVL\_QUERY\_REPORT system view enables you to see information similar to that for SVL\_QUERY\_SUMMARY, only by compute node slice rather than by stream\. You can use the slice\-level information for detecting uneven data distribution across the cluster \(also known as data distribution skew\), which forces some nodes to do more work than others and impairs query performance\.

**Topics**
+ [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)
+ [Using the SVL\_QUERY\_REPORT View](using-SVL-Query-Report.md)
+ [Mapping the Query Plan to the Query Summary](query-plan-summary-map.md)