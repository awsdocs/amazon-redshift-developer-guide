# SVV\_MV\_INFO<a name="r_SVV_MV_INFO"></a>

The SVV\_MV\_INFO table contains a row for every materialized view, whether the data is stale, and state information\. 

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

SVV\_MV\_INFO is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVV_MV_INFO-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_MV_INFO.html)

## Sample query<a name="r_SVV_MV_INFO-sample-query"></a>

To view the state of all materialized views, run the following query\. 

```
select * from svv_mv_info;
```

This query returns the following sample output\. 

```
 
database_name |       schema_name       | user_name |   name  |  is_stale | state | autorefresh | autorewrite
--------------+-------------------------+-----------+---------+-----------+-------+-------------+----------------
 dev          | test_ivm_setup          | catch-22  | mv      |   f       |     1 |           1 |           0
 dev          | test_ivm_setup          | lotr      | old_mv  |   t       |     1 |           0 |           1
```