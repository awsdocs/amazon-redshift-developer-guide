# SVV\_VACUUM\_SUMMARY<a name="r_SVV_VACUUM_SUMMARY"></a>

The SVV\_VACUUM\_SUMMARY view joins the STL\_VACUUM, STL\_QUERY, and STV\_TBL\_PERM tables to summarize information about vacuum operations logged by the system\. The view returns one row per table per vacuum transaction\. The view records the elapsed time of the operation, the number of sort partitions created, the number of merge increments required, and deltas in row and block counts before and after the operation was performed\.

SVV\_VACUUM\_SUMMARY is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVV\_VACUUM\_PROGRESS, see [SVV\_VACUUM\_PROGRESS](r_SVV_VACUUM_PROGRESS.md)\.

For information about SVL\_VACUUM\_PERCENTAGE, see [SVL\_VACUUM\_PERCENTAGE](r_SVL_VACUUM_PERCENTAGE.md)\.

## Table columns<a name="r_SVV_VACUUM_SUMMARY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_VACUUM_SUMMARY.html)

## Sample query<a name="r_SVV_VACUUM_SUMMARY-sample-query"></a>

The following query returns statistics for vacuum operations on three different tables\. The SALES table was vacuumed twice\. 

```
select table_name, xid, sort_partitions as parts, merge_increments as merges,
elapsed_time, row_delta, sortedrow_delta as sorted_delta, block_delta
from svv_vacuum_summary
order by xid;

table_  | xid  |parts|merges| elapsed_ | row_    | sorted_ | block_
name    |      |     |      | time     | delta   | delta   | delta
--------+------+-----+------+----------+---------+---------+--------
users   | 2985 |   1 |    1 | 61919653 |       0 |   49990 |      20
category| 3982 |   1 |    1 | 24136484 |       0 |      11 |       0
sales   | 3992 |   2 |    1 | 71736163 |       0 | 1207192 |      32
sales   | 4000 |   1 |    1 | 15363010 | -851648 | -851648 |    -140
(4 rows)
```