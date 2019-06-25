# Improving Query Performance<a name="query-performance-improvement-opportunities"></a>

Following are some common issues that affect query performance, with instructions on ways to diagnose and resolve them\.

**Topics**
+ [Table Statistics Missing or Out of Date](#table-statistics-missing-or-out-of-date)
+ [Nested Loop](#nested-loop)
+ [Hash Join](#hash-join)
+ [Ghost Rows or Uncommitted Rows](#ghost-rows-or-uncommitted-rows)
+ [Unsorted or Missorted Rows](#unsorted-or-mis-sorted-rows)
+ [Suboptimal Data Distribution](#suboptimal-data-distribution)
+ [Insufficient Memory Allocated to the Query](#insufficient-memory-allocated-to-the-query)
+ [Suboptimal WHERE Clause](#suboptimal-WHERE-clause)
+ [Insufficiently Restrictive Predicate](#insufficiently-restrictive-predicate)
+ [Very Large Result Set](#very-large-result-set)
+ [Large SELECT List](#large-SELECT-list)

## Table Statistics Missing or Out of Date<a name="table-statistics-missing-or-out-of-date"></a>

If table statistics are missing or out of date, you might see the following:
+ A warning message in EXPLAIN command results\.
+ A missing statistics alert event in STL\_ALERT\_EVENT\_LOG\. For more information, see [Reviewing Query Alerts](c-reviewing-query-alerts.md)\.

To fix this issue, run [ANALYZE](r_ANALYZE.md)\.

## Nested Loop<a name="nested-loop"></a>

If a nested loop is present, you might see a nested loop alert event in STL\_ALERT\_EVENT\_LOG\. You can also identify this type of event by running the query at [Identifying Queries with Nested Loops](diagnostic-queries-for-query-tuning.md#identify-queries-with-nested-loops)\. For more information, see [Reviewing Query Alerts](c-reviewing-query-alerts.md)\.

To fix this, review your query for cross\-joins and remove them if possible\. Cross\-joins are joins without a join condition that result in the cartesian product of two tables\. They are typically executed as nested loop joins, which are the slowest of the possible join types\.

## Hash Join<a name="hash-join"></a>

If a hash join is present, you might see the following:
+ Hash and hash join operations in the query plan\. For more information, see [Analyzing the Query Plan](c-analyzing-the-query-plan.md)\.
+ An HJOIN step in the segment with the highest maxtime value in SVL\_QUERY\_SUMMARY\. For more information, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

To fix this issue, you can take a couple of approaches:
+ Rewrite the query to use a merge join if possible\. You can do this by specifying join columns that are both distribution keys and sort keys\.
+ If the HJOIN step in SVL\_QUERY\_SUMMARY has a very high value in the rows field compared to the rows value in the final RETURN step in the query, check whether you can rewrite the query to join on a unique column\. When a query does not join on a unique column, such as a primary key, that increases the number of rows involved in the join\.

## Ghost Rows or Uncommitted Rows<a name="ghost-rows-or-uncommitted-rows"></a>

If ghost rows or uncommitted rows are present, you might see an alert event in STL\_ALERT\_EVENT\_LOG that indicates excessive ghost rows\. For more information, see [Reviewing Query Alerts](c-reviewing-query-alerts.md)\.

To fix this issue, you can take a couple of approaches:
+ Check the **Loads** tab of your Amazon Redshift console for active load operations on any of the query tables\. If you see active load operations, wait for those to complete before taking action\.
+ If there are no active load operations, run [VACUUM](r_VACUUM_command.md) on the query tables to remove deleted rows\.

## Unsorted or Missorted Rows<a name="unsorted-or-mis-sorted-rows"></a>

If unsorted or missorted rows are present, you might see a very selective filter alert event in STL\_ALERT\_EVENT\_LOG\. For more information, see [Reviewing Query Alerts](c-reviewing-query-alerts.md)\.

You can also check to see if any of the tables in your query have large unsorted areas by running the query in [Identifying Tables with Data Skew or Unsorted Rows](diagnostic-queries-for-query-tuning.md#identify-tables-with-data-skew-or-unsorted-rows)\.

To fix this issue, you can take a couple of approaches:
+ Run [VACUUM](r_VACUUM_command.md) on the query tables to re\-sort the rows\.
+ Review the sort keys on the query tables to see if any improvements can be made\. Remember to weigh the performance of this query against the performance of other important queries and the system overall before making any changes\. For more information, see [Choosing Sort Keys](t_Sorting_data.md)\.

## Suboptimal Data Distribution<a name="suboptimal-data-distribution"></a>

If data distribution is suboptimal, you might see the following:
+ A serial execution, large broadcast, or large distribution alert event appears in STL\_ALERT\_EVENT\_LOG\. For more information, see [Reviewing Query Alerts](c-reviewing-query-alerts.md)\.
+ Slices are not processing approximately the same number of rows for a given step\. For more information, see [Using the SVL\_QUERY\_REPORT View](using-SVL-Query-Report.md)\.
+ Slices are not taking approximately the same amount of time for a given step\. For more information, see [Using the SVL\_QUERY\_REPORT View](using-SVL-Query-Report.md)\.

If none of the preceding is true, you can also see if any of the tables in your query have data skew by running the query in [Identifying Tables with Data Skew or Unsorted Rows](diagnostic-queries-for-query-tuning.md#identify-tables-with-data-skew-or-unsorted-rows)\.

To fix this issue, take another look at the distribution styles for the tables in the query and see if any improvements can be made\. Remember to weigh the performance of this query against the performance of other important queries and the system overall before making any changes\. For more information, see [Choosing a Data Distribution Style](t_Distributing_data.md)\.

## Insufficient Memory Allocated to the Query<a name="insufficient-memory-allocated-to-the-query"></a>

If insufficient memory is allocated to your query, you might see a step in SVL\_QUERY\_SUMMARY that has an `is_diskbased` value of true\. For more information, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

To fix this issue, allocate more memory to the query by temporarily increasing the number of query slots it uses\. Workload Management \(WLM\) reserves slots in a query queue equivalent to the concurrency level set for the queue\. For example, a queue with a concurrency level of 5 has 5 slots\. Memory assigned to the queue is allocated equally to each slot\. Assigning several slots to one query gives that query access to the memory for all of those slots\. For more information on how to temporarily increase the slots for a query, see [wlm\_query\_slot\_count](r_wlm_query_slot_count.md)\.

## Suboptimal WHERE Clause<a name="suboptimal-WHERE-clause"></a>

If your WHERE clause causes excessive table scans, you might see a SCAN step in the segment with the highest `maxtime` value in SVL\_QUERY\_SUMMARY\. For more information, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

To fix this issue, add a WHERE clause to the query based on the primary sort column of the largest table\. This approach helps minimize scanning time\. For more information, see [Amazon Redshift Best Practices for Designing Tables](c_designing-tables-best-practices.md)\.

## Insufficiently Restrictive Predicate<a name="insufficiently-restrictive-predicate"></a>

If your query has an insufficiently restrictive predicate, you might see a SCAN step in the segment with the highest `maxtime` value in SVL\_QUERY\_SUMMARY that has a very high `rows` value compared to the `rows` value in the final RETURN step in the query\. For more information, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

To fix this issue, try adding a predicate to the query or making the existing predicate more restrictive to narrow the output\.

## Very Large Result Set<a name="very-large-result-set"></a>

If your query returns a very large result set, consider rewriting the query to use [UNLOAD](r_UNLOAD.md) to write the results to Amazon S3\. This approach improves the performance of the RETURN step by taking advantage of parallel processing\. For more information on checking for a very large result set, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

## Large SELECT List<a name="large-SELECT-list"></a>

If your query has an unusually large SELECT list, you might see a `bytes` value that is high relative to the `rows` value for any step \(in comparison to other steps\) in SVL\_QUERY\_SUMMARY\. This high `bytes` value can be an indicator that you are selecting a lot of columns\. For more information, see [Using the SVL\_QUERY\_SUMMARY View](using-SVL-Query-Summary.md)\.

To fix this issue, review the columns you are selecting and see if any can be removed\.