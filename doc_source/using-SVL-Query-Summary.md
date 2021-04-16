# Using the SVL\_QUERY\_SUMMARY view<a name="using-SVL-Query-Summary"></a>

To analyze query summary information by stream, do the following:

1. Run the following query to determine your query ID:

   ```
   select query, elapsed, substring
   from svl_qlog
   order by query
   desc limit 5;
   ```

   Examine the truncated query text in the `substring` field to determine which `query` value represents your query\. If you have run the query more than once, use the `query` value from the row with the lower `elapsed` value\. That is the row for the compiled version\. If you have been running many queries, you can raise the value used by the LIMIT clause used to make sure that your query is included\.

1. Select rows from SVL\_QUERY\_SUMMARY for your query\. Order the results by stream, segment, and step:

   ```
   select * from svl_query_summary where query = MyQueryID order by stm, seg, step;
   ```  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/svl_query_summary_results.png)

1. Map the steps to the operations in the query plan using the information in [Mapping the query plan to the query summary](query-plan-summary-map.md)\. They should have approximately the same values for rows and bytes \(rows \* width from the query plan\)\. If they don’t, see [Table statistics missing or out of date](query-performance-improvement-opportunities.md#table-statistics-missing-or-out-of-date) for recommended solutions\.

1. See if the `is_diskbased` field has a value of `t` \(true\) for any step\. Hashes, aggregates, and sorts are the operators that are likely to write data to disk if the system doesn't have enough memory allocated for query processing\.

   If `is_diskbased` is true, see [Insufficient memory allocated to the query](query-performance-improvement-opportunities.md#insufficient-memory-allocated-to-the-query) for recommended solutions\.

1. Review the `label` field values and see if there is an AGG\-DIST\-AGG sequence anywhere in the steps\. Its presence indicates two\-step aggregation, which is expensive\. To fix this, change the GROUP BY clause to use the distribution key \(the first key, if there are multiple ones\)\.

1. Review the `maxtime` value for each segment \(it is the same across all steps in the segment\)\. Identify the segment with the highest `maxtime` value and review the steps in this segment for the following operators\.
**Note**  
A high `maxtime` value doesn't necessarily indicate a problem with the segment\. Despite a high value, the segment might not have taken a long time to process\. All segments in a stream start getting timed in unison\. However, some downstream segments might not be able to run until they get data from upstream ones\. This effect might make them seem to have taken a long time because their `maxtime` value includes both their waiting time and their processing time\. 
   + **BCAST or DIST**: In these cases, the high `maxtime` value might be the result of redistributing a large number of rows\. For recommended solutions, see [Suboptimal data distribution](query-performance-improvement-opportunities.md#suboptimal-data-distribution)\.
   + **HJOIN \(hash join\)**: If the step in question has a very high value in the `rows` field compared to the `rows` value in the final RETURN step in the query, see [Hash join](query-performance-improvement-opportunities.md#hash-join) for recommended solutions\.
   + **SCAN/SORT**: Look for a SCAN, SORT, SCAN, MERGE sequence of steps just prior to a join step\. This pattern indicates that unsorted data is being scanned, sorted, and then merged with the sorted area of the table\.

     See if the rows value for the SCAN step has a very high value compared to the rows value in the final RETURN step in the query\. This pattern indicates that the execution engine is scanning rows that are later discarded, which is inefficient\. For recommended solutions, see [Insufficiently restrictive predicate](query-performance-improvement-opportunities.md#insufficiently-restrictive-predicate)\. 

     If the `maxtime` value for the SCAN step is high, see [Suboptimal WHERE clause](query-performance-improvement-opportunities.md#suboptimal-WHERE-clause) for recommended solutions\.

     If the `rows` value for the SORT step is not zero, see [Unsorted or missorted rows](query-performance-improvement-opportunities.md#unsorted-or-mis-sorted-rows) for recommended solutions\.

1. Review the `rows` and `bytes` values for the 5–10 steps that precede the final RETURN step to get an idea of the amount of data that is being returned to the client\. This process can be a bit of an art\.

   For example, in the following query summary, you can see that the third PROJECT step provides a `rows` value but not a `bytes` value\. By looking through the preceding steps for one with the same `rows` value, you find the SCAN step that provides both rows and bytes information:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/rows_and_bytes.png)

   If you are returning an unusually large volume of data, see [Very large result set](query-performance-improvement-opportunities.md#very-large-result-set) for recommended solutions\.

1. See if the `bytes` value is high relative to the `rows` value for any step, in comparison to other steps\. This pattern can indicate that you are selecting a lot of columns\. For recommended solutions, see [Large SELECT list](query-performance-improvement-opportunities.md#large-SELECT-list)\.