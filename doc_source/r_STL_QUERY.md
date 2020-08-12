# STL\_QUERY<a name="r_STL_QUERY"></a>

Returns execution information about a database query\.

**Note**  
The STL\_QUERY and STL\_QUERYTEXT views only contain information about queries, not other utility and DDL commands\. For a listing and information on all statements executed by Amazon Redshift, you can also query the STL\_DDLTEXT and STL\_UTILITYTEXT views\. For a complete listing of all statements executed by Amazon Redshift, you can query the SVL\_STATEMENTTEXT view\.

To manage disk space, the STL log views only retain approximately two to five days of log history, depending on log usage and available disk space\. If you want to retain the log data, you will need to periodically copy it to other tables or unload it to Amazon S3\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="sub-r_STL_QUERY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_QUERY.html)

## Sample queries<a name="r_STL_QUERY-sample-queries"></a>

The following query lists the five most recent queries\.

```
select query, trim(querytxt) as sqlquery
from stl_query
order by query desc limit 5;

query |                                   sqlquery
------+--------------------------------------------------
129 | select query, trim(querytxt) from stl_query order by query;
128 | select node from stv_disk_read_speeds;
127 | select system_status from stv_gui_status
126 | select * from systable_topology order by slice
125 | load global dict registry
(5 rows)
```

The following query returns the time elapsed in descending order for queries that ran on February 15, 2013\. 

```
select query, datediff(seconds, starttime, endtime),
trim(querytxt) as sqlquery
from stl_query
where starttime >= '2013-02-15 00:00' and endtime < '2013-02-15 23:59'
order by date_diff desc;

 query | date_diff |  sqlquery
-------+-----------+-------------------------------------------
 55    |       119 | padb_fetch_sample: select count(*) from category
121    |         9 | select * from svl_query_summary;
181    |         6 | select * from svl_query_summary where query in(179,178);
172    |         5 | select * from svl_query_summary where query=148;
...
(189 rows)
```

The following query shows the queue time and execution time for queries\. Queries with `concurrency_scaling_status = 1` ran on a concurrency scaling cluster\. All other queries ran on the main cluster\.

```
SELECT w.service_class AS queue
     , q.concurrency_scaling_status
     , COUNT( * ) AS queries
     , SUM( q.aborted )  AS aborted
     , SUM( ROUND( total_queue_time::NUMERIC / 1000000,2 ) ) AS queue_secs
     , SUM( ROUND( total_exec_time::NUMERIC / 1000000,2 ) )  AS exec_secs
FROM stl_query q
     JOIN stl_wlm_query w
          USING (userid,query)
WHERE q.userid > 1
  AND service_class > 5
  AND q.starttime > '2019-03-01 16:38:00'
  AND q.endtime   < '2019-03-01 17:40:00'
GROUP BY 1,2
ORDER BY 1,2;
```