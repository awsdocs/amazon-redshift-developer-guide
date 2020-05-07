# Diagnostic queries for query tuning<a name="diagnostic-queries-for-query-tuning"></a>

Use the following queries to identify issues with queries or underlying tables that can affect query performance\. We recommend using these queries in conjunction with the query tuning processes discussed in [Analyzing and improving queries](c-query-tuning.md)\.

**Topics**
+ [Identifying queries that are top candidates for tuning](#identify-queries-that-are-top-candidates-for-tuning)
+ [Identifying tables with data skew or unsorted rows](#identify-tables-with-data-skew-or-unsorted-rows)
+ [Identifying queries with nested loops](#identify-queries-with-nested-loops)
+ [Reviewing queue wait times for queries](#review-queue-wait-times-for-queries)
+ [Reviewing query alerts by table](#review-query-alerts-by-table)
+ [Identifying tables with missing statistics](#identify-tables-with-missing-statistics)

## Identifying queries that are top candidates for tuning<a name="identify-queries-that-are-top-candidates-for-tuning"></a>

The following query identifies the top 50 most time\-consuming statements that have been executed in the last 7 days\. You can use the results to identify queries that are taking unusually long, and also to identify queries that are run frequently \(those that appear more than once in the result set\)\. These queries are frequently good candidates for tuning to improve system performance\.

This query also provides a count of the alert events associated with each query identified\. These alerts provide details that you can use to improve the query’s performance\. For more information, see [Reviewing query alerts](c-reviewing-query-alerts.md)\.

```
select trim(database) as db, count(query) as n_qry, 
max(substring (qrytext,1,80)) as qrytext, 
min(run_minutes) as "min" , 
max(run_minutes) as "max", 
avg(run_minutes) as "avg", sum(run_minutes) as total,  
max(query) as max_query_id, 
max(starttime)::date as last_run, 
sum(alerts) as alerts, aborted
from (select userid, label, stl_query.query, 
trim(database) as database, 
trim(querytxt) as qrytext, 
md5(trim(querytxt)) as qry_md5, 
starttime, endtime, 
(datediff(seconds, starttime,endtime)::numeric(12,2))/60 as run_minutes,     
alrt.num_events as alerts, aborted 
from stl_query 
left outer join 
(select query, 1 as num_events from stl_alert_event_log group by query ) as alrt 
on alrt.query = stl_query.query
where userid <> 1 and starttime >=  dateadd(day, -7, current_date)) 
group by database, label, qry_md5, aborted
order by total desc limit 50;
```

## Identifying tables with data skew or unsorted rows<a name="identify-tables-with-data-skew-or-unsorted-rows"></a>

The following query identifies tables that have uneven data distribution \(data skew\) or a high percentage of unsorted rows\.

A low `skew` value indicates that table data is properly distributed\. If a table has a `skew` value of 4\.00 or higher, consider modifying its data distribution style\. For more information, see [Suboptimal data distribution](query-performance-improvement-opportunities.md#suboptimal-data-distribution)\.

If a table has a `pct_unsorted` value greater than 20 percent, consider running the [VACUUM](r_VACUUM_command.md) command\. For more information, see [Unsorted or missorted rows](query-performance-improvement-opportunities.md#unsorted-or-mis-sorted-rows)\.

Also review the `mbytes` and `pct_of_total` values for each table\. These columns identify the size of the table and what percentage of raw disk space the table consumes\. The raw disk space includes space that is reserved by Amazon Redshift for internal use, so it is larger than the nominal disk capacity, which is the amount of disk space available to the user\. Use this information to ensure that you have free disk space equal to at least 2\.5 times the size of your largest table\. Having this space available enables the system to write intermediate results to disk when processing complex queries\. 

```
select trim(pgn.nspname) as schema, 
trim(a.name) as table, id as tableid, 
decode(pgc.reldiststyle,0, 'even',1,det.distkey ,8,'all') as distkey, dist_ratio.ratio::decimal(10,4) as skew, 
det.head_sort as "sortkey", 
det.n_sortkeys as "#sks", b.mbytes,  
decode(b.mbytes,0,0,((b.mbytes/part.total::decimal)*100)::decimal(5,2)) as pct_of_total, 
decode(det.max_enc,0,'n','y') as enc, a.rows, 
decode( det.n_sortkeys, 0, null, a.unsorted_rows ) as unsorted_rows , 
decode( det.n_sortkeys, 0, null, decode( a.rows,0,0, (a.unsorted_rows::decimal(32)/a.rows)*100) )::decimal(5,2) as pct_unsorted 
from (select db_id, id, name, sum(rows) as rows, 
sum(rows)-sum(sorted_rows) as unsorted_rows 
from stv_tbl_perm a 
group by db_id, id, name) as a 
join pg_class as pgc on pgc.oid = a.id
join pg_namespace as pgn on pgn.oid = pgc.relnamespace
left outer join (select tbl, count(*) as mbytes 
from stv_blocklist group by tbl) b on a.id=b.tbl
inner join (select attrelid, 
min(case attisdistkey when 't' then attname else null end) as "distkey",
min(case attsortkeyord when 1 then attname  else null end ) as head_sort , 
max(attsortkeyord) as n_sortkeys, 
max(attencodingtype) as max_enc 
from pg_attribute group by 1) as det 
on det.attrelid = a.id
inner join ( select tbl, max(mbytes)::decimal(32)/min(mbytes) as ratio 
from (select tbl, trim(name) as name, slice, count(*) as mbytes
from svv_diskusage group by tbl, name, slice ) 
group by tbl, name ) as dist_ratio on a.id = dist_ratio.tbl
join ( select sum(capacity) as  total
from stv_partitions where part_begin=0 ) as part on 1=1
where mbytes is not null 
order by  mbytes desc;
```

## Identifying queries with nested loops<a name="identify-queries-with-nested-loops"></a>

The following query identifies queries that have had alert events logged for nested loops\. For information on how to fix the nested loop condition, see [Nested loop](query-performance-improvement-opportunities.md#nested-loop)\.

```
select query, trim(querytxt) as SQL, starttime 
from stl_query 
where query in (
select distinct query 
from stl_alert_event_log 
where event like 'Nested Loop Join in the query plan%') 
order by starttime desc;
```

## Reviewing queue wait times for queries<a name="review-queue-wait-times-for-queries"></a>

The following query shows how long recent queries waited for an open slot in a query queue before being executed\. If you see a trend of high wait times, you might want to modify your query queue configuration for better throughput\. For more information, see [Implementing manual WLM](cm-c-defining-query-queues.md)\.

```
select trim(database) as DB , w.query, 
substring(q.querytxt, 1, 100) as querytxt,  w.queue_start_time, 
w.service_class as class, w.slot_count as slots, 
w.total_queue_time/1000000 as queue_seconds, 
w.total_exec_time/1000000 exec_seconds, (w.total_queue_time+w.total_Exec_time)/1000000 as total_seconds 
from stl_wlm_query w 
left join stl_query q on q.query = w.query and q.userid = w.userid 
where w.queue_start_Time >= dateadd(day, -7, current_Date) 
and w.total_queue_Time > 0  and w.userid >1   
and q.starttime >= dateadd(day, -7, current_Date) 
order by w.total_queue_time desc, w.queue_start_time desc limit 35;
```

## Reviewing query alerts by table<a name="review-query-alerts-by-table"></a>

The following query identifies tables that have had alert events logged for them, and also identifies what type of alerts are most frequently raised\.

If the `minutes` value for a row with an identified table is high, check that table to see if it needs routine maintenance such as having [ANALYZE](r_ANALYZE.md) or [VACUUM](r_VACUUM_command.md) run against it\.

If the `count` value is high for a row but the `table` value is null, run a query against STL\_ALERT\_EVENT\_LOG for the associated `event` value to investigate why that alert is getting raised so often\.

```
select trim(s.perm_table_name) as table, 
(sum(abs(datediff(seconds, s.starttime, s.endtime)))/60)::numeric(24,0) as minutes, trim(split_part(l.event,':',1)) as event,  trim(l.solution) as solution, 
max(l.query) as sample_query, count(*) 
from stl_alert_event_log as l 
left join stl_scan as s on s.query = l.query and s.slice = l.slice 
and s.segment = l.segment and s.step = l.step
where l.event_time >=  dateadd(day, -7, current_Date) 
group by 1,3,4 
order by 2 desc,6 desc;
```

## Identifying tables with missing statistics<a name="identify-tables-with-missing-statistics"></a>

The following query provides a count of the queries that you are running against tables that are missing statistics\. If this query returns any rows, look at the `plannode` value to determine the affected table, and then run [ANALYZE](r_ANALYZE.md) on it\.

```
select substring(trim(plannode),1,100) as plannode, count(*) 
from stl_explain 
where plannode like '%missing statistics%' 
group by plannode 
order by 2 desc;
```