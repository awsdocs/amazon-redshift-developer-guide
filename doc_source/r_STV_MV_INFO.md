# STV\_MV\_INFO<a name="r_STV_MV_INFO"></a>

The STV\_MV\_INFO table contains a row for every materialized view, whether the data is stale, and state information\. 

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

STV\_MV\_INFO is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_MV_INFO-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_MV_INFO.html)

## Sample query<a name="r_STV_MV_INFO-sample-query"></a>

To view the state of all materialized views, run the following query\. 

```
select * from stv_mv_info;
```

This query returns the following sample output\. 

```
 db_name |       schema   |   name  | updated_upto_xid | is_stale | owner_user_name | state
---------+----------------+---------+------------------+------+---------------------+-------
 dev     | test_setup     | mv      |             1031 | f        | johndoea        |     1
 dev     | test_setup     | old_mv  |              988 | t        | paul            |     1
```