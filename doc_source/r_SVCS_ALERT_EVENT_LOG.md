# SVCS\_ALERT\_EVENT\_LOG<a name="r_SVCS_ALERT_EVENT_LOG"></a>

Records an alert when the query optimizer identifies conditions that might indicate performance issues\. This view is derived from the STL\_ALERT\_EVENT\_LOG system table but doesn't show slice\-level for queries run on a concurrency scaling cluster\. Use the SVCS\_ALERT\_EVENT\_LOG table to identify opportunities to improve query performance\.

A query consists of multiple segments, and each segment consists of one or more steps\. For more information, see [Query processing](c-query-processing.md)\. 

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the tables with the prefix STL except that the STL tables provide information only for queries run on the main cluster\.

SVCS\_ALERT\_EVENT\_LOG is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVCS_ALERT_EVENT_LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_ALERT_EVENT_LOG.html)

## Usage notes<a name="r_SVCS_ALERT_EVENT_LOG-usage-notes"></a>

You can use the SVCS\_ALERT\_EVENT\_LOG to identify potential issues in your queries, then follow the practices in [Tuning query performance](c-optimizing-query-performance.md) to optimize your database design and rewrite your queries\. SVCS\_ALERT\_EVENT\_LOG records the following alerts: 
+ **Missing statistics** 

  Statistics are missing\. Run ANALYZE following data loads or significant updates and use STATUPDATE with COPY operations\. For more information, see [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md)\.
+ **Nested loop **

  A nested loop is usually a Cartesian product\. Evaluate your query to ensure that all participating tables are joined efficiently\.
+ **Very selective filter**

  The ratio of rows returned to rows scanned is less than 0\.05\. Rows scanned is the value of `rows_pre_user_filter `and rows returned is the value of rows in the [STL\_SCAN](r_STL_SCAN.md) system table\. Indicates that the query is scanning an unusually large number of rows to determine the result set\. This can be caused by missing or incorrect sort keys\. For more information, see [Choosing sort keys](t_Sorting_data.md)\. 
+ **Excessive ghost rows **

  A scan skipped a relatively large number of rows that are marked as deleted but not vacuumed, or rows that have been inserted but not committed\. For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\. 
+ **Large distribution **

  More than 1,000,000 rows were redistributed for hash join or aggregation\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\. 
+ **Large broadcast **

  More than 1,000,000 rows were broadcast for hash join\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\. 
+ **Serial execution **

   A DS\_DIST\_ALL\_INNER redistribution style was indicated in the query plan, which forces serial execution because the entire inner table was redistributed to a single node\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\.

## Sample queries<a name="r_SVCS_ALERT_EVENT_LOG-sample-queries"></a>

The following query shows alert events for four queries\. 

```
SELECT query, substring(event,0,25) as event, 
substring(solution,0,25) as solution, 
trim(event_time) as event_time from svcs_alert_event_log order by query;

 query |             event             |          solution            |     event_time      
-------+-------------------------------+------------------------------+---------------------
  6567 | Missing query planner statist | Run the ANALYZE command      | 2014-01-03 18:20:58
  7450 | Scanned a large number of del | Run the VACUUM command to rec| 2014-01-03 21:19:31
  8406 | Nested Loop Join in the query | Review the join predicates to| 2014-01-04 00:34:22
 29512 | Very selective query filter:r | Review the choice of sort key| 2014-01-06 22:00:00

(4 rows)
```