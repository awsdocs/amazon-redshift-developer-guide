# STL\_ANALYZE<a name="r_STL_ANALYZE"></a>

Records details for [ANALYZE](r_ANALYZE.md) operations\.

This view is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_ANALYZE-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_ANALYZE.html)

## Sample queries<a name="r_STL_ANALYZE-sample-queries2"></a>

The following example joins STV\_TBL\_PERM to show the table name and execution details\. 

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