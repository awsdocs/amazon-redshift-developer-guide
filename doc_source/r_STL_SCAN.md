# STL\_SCAN<a name="r_STL_SCAN"></a>

Analyzes table scan steps for queries\. The step number for rows in this table is always 0 because a scan is the first step in a segment\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_SCAN-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SCAN.html)

## Scan types<a name="r_STL_SCAN-scan-types"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SCAN.html)

## Usage notes<a name="w31aac53c13c11c93c11"></a>

Ideally `rows` should be relatively close to `rows_pre_filter`\. A large difference between `rows` and `rows_pre_filter` is an indication that the execution engine is scanning rows that are later discarded, which is inefficient\. The difference between `rows_pre_filter` and `rows_pre_user_filter` is the number of ghost rows in the scan\. Run a VACUUM to remove rows marked for deletion\. The difference between `rows` and `rows_pre_user_filter` is the number of rows filtered by the query\. If a lot of rows are discarded by the user filter, review your choice of sort column or, if this is due to a large unsorted region, run a vacuum\.

## Sample queries<a name="r_STL_SCAN-sample-queries"></a>

The following example shows that `rows_pre_filter` is larger than `rows_pre_user_filter` because the table has deleted rows that have not been vacuumed \(ghost rows\)\. 

```
SELECT slice, segment,step,rows, rows_pre_filter, rows_pre_user_filter 
from stl_scan where query = pg_last_query_id();

 query |  slice | segment | step | rows  | rows_pre_filter | rows_pre_user_filter
-------+--------+---------+------+-------+-----------------+----------------------
 42915 |      0 |       0 |    0 | 43159 |           86318 |                43159
 42915 |      0 |       1 |    0 |     1 |               0 |                    0
 42915 |      1 |       0 |    0 | 43091 |           86182 |                43091
 42915 |      1 |       1 |    0 |     1 |               0 |                    0
 42915 |      2 |       0 |    0 | 42778 |           85556 |                42778
 42915 |      2 |       1 |    0 |     1 |               0 |                    0
 42915 |      3 |       0 |    0 | 43428 |           86856 |                43428
 42915 |      3 |       1 |    0 |     1 |               0 |                    0
 42915 |  10000 |       2 |    0 |     4 |               0 |                    0
(9 rows)
```