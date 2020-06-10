# SVV\_INTERLEAVED\_COLUMNS<a name="r_SVV_INTERLEAVED_COLUMNS"></a>

Use the SVV\_INTERLEAVED\_COLUMNS view to help determine whether a table that uses interleaved sort keys should be reindexed using [VACUUM REINDEX](r_VACUUM_command.md#vacuum-reindex)\. For more information about how to determine how often to run VACUUM and when to run a VACUUM REINDEX, see [Managing vacuum times](vacuum-managing-vacuum-times.md)\.

SVV\_INTERLEAVED\_COLUMNS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="SVV_INTERLEAVED_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_INTERLEAVED_COLUMNS.html)

## Sample queries<a name="SVV_INTERLEAVED_COLUMNS-sample-queries"></a>

To identify tables that might need to be reindexed, execute the following query\.

```
select tbl as tbl_id, stv_tbl_perm.name as table_name, 
col, interleaved_skew, last_reindex
from svv_interleaved_columns, stv_tbl_perm
where svv_interleaved_columns.tbl = stv_tbl_perm.id
and interleaved_skew is not null;

 tbl_id | table_name | col | interleaved_skew | last_reindex
--------+------------+-----+------------------+--------------------
 100068 | lineorder  |   0 |             3.65 | 2015-04-22 22:05:45
 100068 | lineorder  |   1 |             2.65 | 2015-04-22 22:05:45
 100072 | customer   |   0 |             1.65 | 2015-04-22 22:05:45
 100072 | lineorder  |   1 |             1.00 | 2015-04-22 22:05:45
(4 rows)
```