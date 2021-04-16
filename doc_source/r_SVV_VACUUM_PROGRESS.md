# SVV\_VACUUM\_PROGRESS<a name="r_SVV_VACUUM_PROGRESS"></a>

This view returns an estimate of how much time it will take to complete a vacuum operation that is currently in progress\.

SVV\_VACUUM\_PROGRESS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVV\_VACUUM\_SUMMARY, see [SVV\_VACUUM\_SUMMARY](r_SVV_VACUUM_SUMMARY.md)\.

For information about SVL\_VACUUM\_PERCENTAGE, see [SVL\_VACUUM\_PERCENTAGE](r_SVL_VACUUM_PERCENTAGE.md)\.

## Table columns<a name="sub-r_SVV_VACUUM_PROGRESS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_VACUUM_PROGRESS.html)

## Sample queries<a name="r_SVV_VACUUM_PROGRESS-sample-queries"></a>

The following queries, run a few minutes apart, show that a large table named SALESNEW is being vacuumed\. 

```
select * from svv_vacuum_progress;

table_name    |            status             | time_remaining_estimate
--------------+-------------------------------+-------------------------
salesnew      |  Vacuum: initialize salesnew  |
(1 row)
...
select * from svv_vacuum_progress;

table_name   |         status         | time_remaining_estimate
-------------+------------------------+-------------------------
salesnew     |  Vacuum salesnew sort  | 33m 21s
(1 row)
```

The following query shows that no vacuum operation is currently in progress\. The last table to be vacuumed was the SALES table\. 

```
select * from svv_vacuum_progress;

table_name   |  status  | time_remaining_estimate
-------------+----------+-------------------------
  sales      | Complete |
(1 row)
```