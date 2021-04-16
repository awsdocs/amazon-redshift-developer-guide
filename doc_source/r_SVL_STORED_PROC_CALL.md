# SVL\_STORED\_PROC\_CALL<a name="r_SVL_STORED_PROC_CALL"></a>

You can query the system view SVL\_STORED\_PROC\_CALL to get information about stored procedure calls, including start time, end time, and whether a call is aborted\. Each stored procedure call receives a query ID\.

SVL\_STORED\_PROC\_CALL is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_STORED_PROC_CALL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_STORED_PROC_CALL.html)

## Sample query<a name="r_SVL_STORED_PROC_CALL-sample-query"></a>

The following query returns the elapsed time in descending order and the completion status for stored procedure calls in the past day\.

```
select query, datediff(seconds, starttime, endtime) as elapsed_time, aborted, trim(querytxt) as call from svl_stored_proc_call where starttime >= current_timestamp - interval '1 day' order by 2 desc;

  query | elapsed_time | aborted |                                       call
--------+--------------+---------+-----------------------------------------------------------------------------------
   4166 |            7 |       0 | call search_batch_status(35,'succeeded');
   2433 |            3 |       0 | call test_batch (123456)
   1810 |            1 |       0 | call prod_benchmark (123456)
   1836 |            1 |       0 | call prod_testing (123456)
   1808 |            1 |       0 | call prod_portfolio ('N', 123456)
   1816 |            1 |       1 | call prod_portfolio ('Y', 123456)
(6 rows)
```