# SVL\_MV\_REFRESH\_STATUS<a name="r_SVL_MV_REFRESH_STATUS"></a>

The SVL\_MV\_REFRESH\_STATUS view contains a row for the refresh activity of materialized views\. 

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

SVL\_MV\_REFRESH\_STATUS is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_MV_REFRESH_STATUS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_MV_REFRESH_STATUS.html)

## Sample query<a name="r_SVL_MV_REFRESH-sample-query"></a>

To view the refresh status of materialized views, run the following query\. 

```
select * from svl_mv_refresh_status;
```

This query returns the following sample output: 

```
 db_name | userid |  schema   |  name   |  xid  |         starttime          |          endtime           |                        status
---------+--------+-----------+---------+-------+----------------------------+----------------------------+------------------------------------------------------
 dev     |    169 | mv_schema | mv_test |  6640 | 2020-02-14 02:26:53.497935 | 2020-02-14 02:26:53.556156 | Refresh successfully recomputed MV from scratch
 dev     |    166 | mv_schema | mv_test |  6517 | 2020-02-14 02:26:39.287438 | 2020-02-14 02:26:39.349539 | Refresh successfully updated MV incrementally
 dev     |    162 | mv_schema | mv_test |  6388 | 2020-02-14 02:26:27.863426 | 2020-02-14 02:26:27.918307 | Refresh successfully recomputed MV from scratch
 dev     |    161 | mv_schema | mv_test |  6323 | 2020-02-14 02:26:20.020717 | 2020-02-14 02:26:20.080002 | Refresh successfully updated MV incrementally
 dev     |    161 | mv_schema | mv_test |  6301 | 2020-02-14 02:26:05.796146 | 2020-02-14 02:26:07.853986 | Refresh successfully recomputed MV from scratch
 dev     |    153 | mv_schema | mv_test |  6024 | 2020-02-14 02:25:18.762335 | 2020-02-14 02:25:20.043462 | MV was already updated
 dev     |    143 | mv_schema | mv_test |  5557 | 2020-02-14 02:24:23.100601 | 2020-02-14 02:24:23.100633 | MV was already updated
 dev     |    141 | mv_schema | mv_test |  5447 | 2020-02-14 02:23:54.102837 | 2020-02-14 02:24:00.310166 | Refresh successfully updated MV incrementally
 dev     |      1 | mv_schema | mv_test |  5329 | 2020-02-14 02:22:26.328481 | 2020-02-14 02:22:28.369217 | Refresh successfully recomputed MV from scratch
 dev     |    138 | mv_schema | mv_test |  5290 | 2020-02-14 02:21:56.885093 | 2020-02-14 02:21:56.885098 | Refresh failed. MV was not found
```