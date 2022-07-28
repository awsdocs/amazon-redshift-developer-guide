# STL\_LOAD\_COMMITS<a name="r_STL_LOAD_COMMITS"></a>

Returns information to track or troubleshoot a data load\.

This view records the progress of each data file as it is loaded into a database table\. 

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\. 

## Table columns<a name="r_STL_LOAD_COMMITS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_LOAD_COMMITS.html)

## Sample queries<a name="r_STL_LOAD_COMMITS-sample-queries"></a>

The following example returns details for the last COPY operation\. 

```
select query, trim(filename) as file, curtime as updated
from stl_load_commits
where query = pg_last_copy_id();

 query |               file               |          updated           
-------+----------------------------------+----------------------------
 28554 | s3://dw-tickit/category_pipe.txt | 2013-11-01 17:14:52.648486 
(1 row)
```

The following query contains entries for a fresh load of the tables in the TICKIT database: 

```
select query, trim(filename), curtime
from stl_load_commits
where filename like '%tickit%' order by query;
```

```
 query |           btrim           |          curtime           
-------+---------------------------+----------------------------
 22475 | tickit/allusers_pipe.txt  | 2013-02-08 20:58:23.274186 
 22478 | tickit/venue_pipe.txt     | 2013-02-08 20:58:25.070604 
 22480 | tickit/category_pipe.txt  | 2013-02-08 20:58:27.333472 
 22482 | tickit/date2008_pipe.txt  | 2013-02-08 20:58:28.608305 
 22485 | tickit/allevents_pipe.txt | 2013-02-08 20:58:29.99489  
 22487 | tickit/listings_pipe.txt  | 2013-02-08 20:58:37.632939 
 22593 | tickit/allusers_pipe.txt  | 2013-02-08 21:04:08.400491 
 22596 | tickit/venue_pipe.txt     | 2013-02-08 21:04:10.056055 
 22598 | tickit/category_pipe.txt  | 2013-02-08 21:04:11.465049 
 22600 | tickit/date2008_pipe.txt  | 2013-02-08 21:04:12.461502 
 22603 | tickit/allevents_pipe.txt | 2013-02-08 21:04:14.785124 
 22605 | tickit/listings_pipe.txt  | 2013-02-08 21:04:20.170594 

(12 rows)
```

The fact that a record is written to the log file for this system view does not mean that the load committed successfully as part of its containing transaction\. To verify load commits, query the STL\_UTILITYTEXT view and look for the COMMIT record that corresponds with a COPY transaction\. For example, this query joins STL\_LOAD\_COMMITS and STL\_QUERY based on a subquery against STL\_UTILITYTEXT: 

```
select l.query,rtrim(l.filename),q.xid
from stl_load_commits l, stl_query q
where l.query=q.query
and exists
(select xid from stl_utilitytext where xid=q.xid and rtrim("text")='COMMIT');

 query |           rtrim           |  xid
-------+---------------------------+-------
 22600 | tickit/date2008_pipe.txt  | 68311
 22480 | tickit/category_pipe.txt  | 68066
  7508 | allusers_pipe.txt         | 23365
  7552 | category_pipe.txt         | 23415
  7576 | allevents_pipe.txt        | 23429
  7516 | venue_pipe.txt            | 23390
  7604 | listings_pipe.txt         | 23445
 22596 | tickit/venue_pipe.txt     | 68309
 22605 | tickit/listings_pipe.txt  | 68316
 22593 | tickit/allusers_pipe.txt  | 68305
 22485 | tickit/allevents_pipe.txt | 68071
  7561 | allevents_pipe.txt        | 23429
  7541 | category_pipe.txt         | 23415
  7558 | date2008_pipe.txt         | 23428
 22478 | tickit/venue_pipe.txt     | 68065
   526 | date2008_pipe.txt         |  2572
  7466 | allusers_pipe.txt         | 23365
 22482 | tickit/date2008_pipe.txt  | 68067
 22598 | tickit/category_pipe.txt  | 68310
 22603 | tickit/allevents_pipe.txt | 68315
 22475 | tickit/allusers_pipe.txt  | 68061
   547 | date2008_pipe.txt         |  2572
 22487 | tickit/listings_pipe.txt  | 68072
  7531 | venue_pipe.txt            | 23390
  7583 | listings_pipe.txt         | 23445
(25 rows)
```

The following examples highlight is\_partial and start\_offset column values\.

```
-- Single large file copy without scan range
SELECT count(*) FROM stl_load_commits WHERE query = pg_last_copy_id();
1

-- Single large uncompressed, delimited file copy with scan range
SELECT count(*) FROM stl_load_commits WHERE query = pg_last_copy_id();
16

-- Scan range offset logging in the file at 64MB boundary. 
SELECT start_offset FROM stl_load_commits
WHERE query = pg_last_copy_id() ORDER BY start_offset;
0
67108864
134217728
201326592
268435456
335544320
402653184
469762048
536870912
603979776
671088640
738197504
805306368
872415232
939524096
1006632960
```