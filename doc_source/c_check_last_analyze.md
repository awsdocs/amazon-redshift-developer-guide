# ANALYZE command history<a name="c_check_last_analyze"></a>

It's useful to know when the last ANALYZE command was run on a table or database\. When an ANALYZE command is run, Amazon Redshift executes multiple queries that look like this: 

```
padb_fetch_sample: select * from table_name
```

Query STL\_ANALYZE to view the history of analyze operations\. If Amazon Redshift analyzes a table using automatic analyze, the `is_background` column is set to `t` \(true\)\. Otherwise, it is set to `f` \(false\)\. The following example joins STV\_TBL\_PERM to show the table name and execution details\.

```
select distinct a.xid, trim(t.name) as name, a.status, a.rows, a.modified_rows, a.starttime, a.endtime
from stl_analyze a 
join stv_tbl_perm t  on t.id=a.table_id
where name = 'users'
order by starttime;

xid    | name  | status          | rows  | modified_rows | starttime           | endtime            
-------+-------+-----------------+-------+---------------+---------------------+--------------------
  1582 | users | Full            | 49990 |         49990 | 2016-09-22 22:02:23 | 2016-09-22 22:02:28
244287 | users | Full            | 24992 |         74988 | 2016-10-04 22:50:58 | 2016-10-04 22:51:01
244712 | users | Full            | 49984 |         24992 | 2016-10-04 22:56:07 | 2016-10-04 22:56:07
245071 | users | Skipped         | 49984 |             0 | 2016-10-04 22:58:17 | 2016-10-04 22:58:17
245439 | users | Skipped         | 49984 |          1982 | 2016-10-04 23:00:13 | 2016-10-04 23:00:13
(5 rows)
```

Alternatively, you can run a more complex query that returns all the statements that ran in every completed transaction that included an ANALYZE command: 

```
select xid, to_char(starttime, 'HH24:MM:SS.MS') as starttime,
datediff(sec,starttime,endtime ) as secs, substring(text, 1, 40)
from svl_statementtext
where sequence = 0
and xid in (select xid from svl_statementtext s where s.text like 'padb_fetch_sample%' )
order by xid desc, starttime;

xid  |  starttime   | secs |                  substring
-----+--------------+------+------------------------------------------
1338 | 12:04:28.511 |    4 | Analyze date
1338 | 12:04:28.511 |    1 | padb_fetch_sample: select count(*) from
1338 | 12:04:29.443 |    2 | padb_fetch_sample: select * from date
1338 | 12:04:31.456 |    1 | padb_fetch_sample: select * from date
1337 | 12:04:24.388 |    1 | padb_fetch_sample: select count(*) from
1337 | 12:04:24.388 |    4 | Analyze sales
1337 | 12:04:25.322 |    2 | padb_fetch_sample: select * from sales
1337 | 12:04:27.363 |    1 | padb_fetch_sample: select * from sales
...
```