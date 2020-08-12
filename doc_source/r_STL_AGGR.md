# STL\_AGGR<a name="r_STL_AGGR"></a>

Analyzes aggregate execution steps for queries\. These steps occur during execution of aggregate functions and GROUP BY clauses\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_AGGR-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_AGGR.html)

## Sample queries<a name="r_STL_AGGR-sample-queries2"></a>

Returns information about aggregate execution steps for SLICE 1 and TBL 239\. 

```
select query, segment, bytes, slots, occupied, maxlength, is_diskbased, workmem, type
from stl_aggr where slice=1 and tbl=239
order by rows
limit 10;
```

```
 query | segment | bytes |  slots  | occupied | maxlength | is_diskbased |  workmem  |  type
-------+---------+-------+---------+----------+-----------+--------------+-----------+--------
   562 |       1 |     0 | 4194304 |        0 |         0 | f            | 383385600 | HASHED
   616 |       1 |     0 | 4194304 |        0 |         0 | f            | 383385600 | HASHED
   546 |       1 |     0 | 4194304 |        0 |         0 | f            | 383385600 | HASHED
   547 |       0 |     8 |       0 |        0 |         0 | f            |         0 | PLAIN
   685 |       1 |    32 | 4194304 |        1 |         0 | f            | 383385600 | HASHED
   652 |       0 |     8 |       0 |        0 |         0 | f            |         0 | PLAIN
   680 |       0 |     8 |       0 |        0 |         0 | f            |         0 | PLAIN
   658 |       0 |     8 |       0 |        0 |         0 | f            |         0 | PLAIN
   686 |       0 |     8 |       0 |        0 |         0 | f            |         0 | PLAIN
   695 |       1 |    32 | 4194304 |        1 |         0 | f            | 383385600 | HASHED
(10 rows)
```