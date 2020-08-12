# STL\_INSERT<a name="r_STL_INSERT"></a>

Analyzes insert execution steps for queries\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_INSERT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_INSERT.html)

## Sample queries<a name="r_STL_INSERT-sample-queries"></a>

The following example returns insert execution steps for the most recent query\. 

```
select slice, segment, step, tasknum, rows, tbl
from stl_insert 
where query=pg_last_query_id();
```

```
 slice | segment | step | tasknum | rows  |  tbl
-------+---------+------+---------+-------+--------
     0 |       2 |    2 |      15 | 24958 | 100548
     1 |       2 |    2 |      15 | 25032 | 100548
(2 rows)
```