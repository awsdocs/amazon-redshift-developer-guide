# Monitoring concurrency scaling<a name="concurrency-scaling-monitoring"></a>

You can see whether a query is running on the main cluster or a concurrency scaling cluster by viewing the Amazon Redshift console, navigating to **Cluster**, and choosing a cluster\. Then choose the **Queries** tab and view the values in the column **Executed on** to determine the cluster where the query ran\.

To find execution times, query the STL\_QUERY table and filter on the `concurrency_scaling_status` column\. The following query compares the queue time and execution time for queries run on the concurrency scaling cluster and queries run on the main cluster\.

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
  AND q.starttime > '2019-01-04 16:38:00'
  AND q.endtime < '2019-01-04 17:40:00'
GROUP BY 1,2
ORDER BY 1,2;
```

