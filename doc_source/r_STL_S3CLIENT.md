# STL\_S3CLIENT<a name="r_STL_S3CLIENT"></a>

Records transfer time and other performance metrics\.

Use the STL\_S3CLIENT table to find the time spent transferring data from Amazon S3 as part of a COPY command\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_S3CLIENT-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_S3CLIENT.html)

## Sample query<a name="r_STL_S3CLIENT-sample-query2"></a>

The following query returns the time taken to load files using a COPY command\.

```
select slice, key, transfer_time 
from stl_s3client 
where query = pg_last_copy_id();
```

Result

```
 slice |   key                       | transfer_time
 ------+-----------------------------+---------------
     0 | listing10M0003_part_00      |    16626716
     1 | listing10M0001_part_00      |    12894494
     2 | listing10M0002_part_00      |    14320978
     3 | listing10M0000_part_00      |    11293439
  3371 | prefix=listing10M;marker=   |       99395
```

The following query converts the `start_time` and `end_time` to a timestamp\. 

```
select userid,query,slice,pid,recordtime,start_time,end_time,
'2000-01-01'::timestamp + (start_time/1000000.0)* interval '1 second' as start_ts,
'2000-01-01'::timestamp + (end_time/1000000.0)* interval '1 second' as end_ts 
from stl_s3client where query> -1 limit 5;
```

```
 userid | query | slice |  pid  |         recordtime         |   start_time    |    end_time     |          start_ts          |           end_ts           
--------+-------+-------+-------+----------------------------+-----------------+-----------------+----------------------------+----------------------------
      0 |     0 |     0 | 23449 | 2019-07-14 16:27:17.207839 | 616436837154256 | 616436837207838 | 2019-07-14 16:27:17.154256 | 2019-07-14 16:27:17.207838
      0 |     0 |     0 | 23449 | 2019-07-14 16:27:17.252521 | 616436837208208 | 616436837252520 | 2019-07-14 16:27:17.208208 | 2019-07-14 16:27:17.25252
      0 |     0 |     0 | 23449 | 2019-07-14 16:27:17.284376 | 616436837208460 | 616436837284374 | 2019-07-14 16:27:17.20846  | 2019-07-14 16:27:17.284374
      0 |     0 |     0 | 23449 | 2019-07-14 16:27:17.285307 | 616436837208980 | 616436837285306 | 2019-07-14 16:27:17.20898  | 2019-07-14 16:27:17.285306
      0 |     0 |     0 | 23449 | 2019-07-14 16:27:17.353853 | 616436837302216 | 616436837353851 | 2019-07-14 16:27:17.302216 | 2019-07-14 16:27:17.353851
```