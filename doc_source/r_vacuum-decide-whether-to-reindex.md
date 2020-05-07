# Deciding whether to reindex<a name="r_vacuum-decide-whether-to-reindex"></a>

You can often significantly improve query performance by using an interleaved sort style, but over time performance might degrade if the distribution of the values in the sort key columns changes\. 

When you initially load an empty interleaved table using COPY or CREATE TABLE AS, Amazon Redshift automatically builds the interleaved index\. If you initially load an interleaved table using INSERT, you need to run VACUUM REINDEX afterwards to initialize the interleaved index\. 

Over time, as you add rows with new sort key values, performance might degrade if the distribution of the values in the sort key columns changes\. If your new rows fall primarily within the range of existing sort key values, you don’t need to reindex\. Run VACUUM SORT ONLY or VACUUM FULL to restore the sort order\. 

The query engine is able to use sort order to efficiently select which data blocks need to be scanned to process a query\. For an interleaved sort, Amazon Redshift analyzes the sort key column values to determine the optimal sort order\. If the distribution of key values changes, or skews, as rows are added, the sort strategy will no longer be optimal, and the performance benefit of sorting will degrade\. To reanalyze the sort key distribution you can run a VACUUM REINDEX\. The reindex operation is time consuming, so to decide whether a table will benefit from a reindex, query the [SVV\_INTERLEAVED\_COLUMNS](r_SVV_INTERLEAVED_COLUMNS.md) view\. 

For example, the following query shows details for tables that use interleaved sort keys\.

```
select tbl as tbl_id, stv_tbl_perm.name as table_name, 
col, interleaved_skew, last_reindex
from svv_interleaved_columns, stv_tbl_perm
where svv_interleaved_columns.tbl = stv_tbl_perm.id
and interleaved_skew is not null;

 tbl_id | table_name | col | interleaved_skew | last_reindex
--------+------------+-----+------------------+--------------------
 100048 | customer   |   0 |             3.65 | 2015-04-22 22:05:45
 100068 | lineorder  |   1 |             2.65 | 2015-04-22 22:05:45
 100072 | part       |   0 |             1.65 | 2015-04-22 22:05:45
 100077 | supplier   |   1 |             1.00 | 2015-04-22 22:05:45
(4 rows)
```

The value for `interleaved_skew` is a ratio that indicates the amount of skew\. A value of 1 means there is no skew\. If the skew is greater than 1\.4, a VACUUM REINDEX will usually improve performance unless the skew is inherent in the underlying set\. 

You can use the date value in `last_reindex` to determine how long it has been since the last reindex\. 