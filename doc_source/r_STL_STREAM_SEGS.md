# STL\_STREAM\_SEGS<a name="r_STL_STREAM_SEGS"></a>

Lists the relationship between streams and concurrent segments\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_STREAM_SEGS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_STREAM_SEGS.html)

## Sample queries<a name="r_STL_STREAM_SEGS-sample-queries"></a>

To view the relationship between streams and concurrent segments for the most recent query, type the following query: 

```
select *
from stl_stream_segs
where query = pg_last_query_id();

 query | stream | segment
-------+--------+---------
    10 |      1 |       2
    10 |      0 |       0
    10 |      2 |       4
    10 |      1 |       3
    10 |      0 |       1
(5 rows)
```