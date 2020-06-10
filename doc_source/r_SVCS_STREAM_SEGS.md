# SVCS\_STREAM\_SEGS<a name="r_SVCS_STREAM_SEGS"></a>

Lists the relationship between streams and concurrent segments\.

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the tables with the prefix STL except that the STL tables provide information only for queries run on the main cluster\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVCS_STREAM_SEGS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_STREAM_SEGS.html)

## Sample queries<a name="r_SVCS_STREAM_SEGS-sample-queries"></a>

To view the relationship between streams and concurrent segments for the most recent query, type the following query: 

```
select *
from svcs_stream_segs
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