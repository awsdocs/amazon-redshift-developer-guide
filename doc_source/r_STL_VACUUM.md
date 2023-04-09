# STL\_VACUUM<a name="r_STL_VACUUM"></a>

Displays row and block statistics for tables that have been vacuumed\.

The view shows information specific to when each vacuum operation started and finished, and demonstrates the benefits of running the operation\. For information about the requirements for running this command, see the [VACUUM](r_VACUUM_command.md) command description\.

This view is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_VACUUM-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_VACUUM.html)

## Sample queries<a name="r_STL_VACUUM-sample-queries"></a>

The following query reports vacuum statistics for table 108313\. The table was vacuumed following a series of inserts and deletes\. 

```
select xid, table_id, status, rows, sortedrows, blocks, eventtime,
                reclaimable_rows, reclaimable_space_mb
from stl_vacuum where table_id=108313 order by eventtime;

 xid   | table_id | status                  | rows | sortedrows | blocks |  eventtime           | reclaimable_rows | reclaimable_space_mb 
-------+----------+-------------------------+------+------------+--------+----------------------+------------------+----------------------
 14294 |   108313 | Started                 | 1950 |        408 |     28 |  2016-05-19 17:36:01 |              984 |                   17          
 14294 |   108313 | Finished                |  966 |        966 |     11 |  2016-05-19 18:26:13 |                0 |                    0 
 15126 |   108313 | Skipped(sorted>=95%)    |  966 |        966 |     11 |  2016-05-19 18:26:38 |                0 |                    0
```

At the start of the VACUUM, the table contained 1,950 rows stored in 28 1 MB blocks\. Amazon Redshift estimated it could reclaim 984, or 17 blocks of disk space, with a vacuum operation\.

In the row for the Finished status, the ROWS column shows a value of 966, and the BLOCKS column value is 11, down from 28\. The vacuum reclaimed the estimated amount of disk space, with no reclaimable rows or space remaining after the vacuum operation completed\.

In the sort phase \(transaction 15126\), the vacuum was able to skip the table because the rows were inserted in sort key order\.

The following example shows the statistics for a SORT ONLY vacuum on the SALES table \(table 110116 in this example\) after a large INSERT operation: 

```
vacuum sort only sales;

select xid, table_id, status, rows, sortedrows, blocks, eventtime
from stl_vacuum order by xid, table_id, eventtime;

xid |table_id|     status      | rows  |sortedrows|blocks|     eventtime
----+--------+-----------------+-------+----------+------+--------------------
...
2925| 110116 |Started Sort Only|1379648|   172456 |  132 | 2011-02-24 16:25:21...
2925| 110116 |Finished         |1379648|  1379648 |  132 | 2011-02-24 16:26:28...
```