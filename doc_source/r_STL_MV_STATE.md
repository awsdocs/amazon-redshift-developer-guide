# STL\_MV\_STATE<a name="r_STL_MV_STATE"></a>

The STL\_MV\_STATE view contains a row for every state transition of a materialized view\. 

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

STL\_MV\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_MV_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_MV_STATE.html)

The following table shows example combinations of `event_desc` and `state`\.

```
          event_desc     |     state
-------------------------+---------------
 TRUNCATE                | Recompute
 TRUNCATE                | Recompute
 Small table conversion  | Recompute
 Vacuum                  | Recompute
 Column was renamed      | Unrefreshable
 Column was dropped      | Unrefreshable
 Table was renamed       | Unrefreshable
 Column type was changed | Unrefreshable
 Schema name was changed | Unrefreshable
```

## Sample query<a name="r_STL_MV_STATE-sample-query"></a>

To view the log of state transitions of materialized views, run the following query\. 

```
select * from stl_mv_state;
```

This query returns the following sample output: 

```
 userid |         starttime          | xid  |            event_desc       | db_name |  base_table_schema   |   base_table_name    |      mv_schema       | mv_name       |     state
--------+----------------------------+------+-----------------------------+---------+----------------------+----------------------+----------------------+---------------+---------------
    138 | 2020-02-14 02:21:25.578885 | 5180 | TRUNCATE                    | dev     | public               | mv_base_table        | public               | mv_test       | Recompute
    138 | 2020-02-14 02:21:56.846774 | 5275 | Column was dropped          | dev     |                      | mv_base_table        | public               | mv_test       | Unrefreshable
    100 | 2020-02-13 22:09:53.041228 | 1794 | Column was renamed          | dev     |                      | mv_base_table        | public               | mv_test       | Unrefreshable
      1 | 2020-02-13 22:10:23.630914 | 1893 | ALTER TABLE ALTER SORTKEY   | dev     | public               | mv_base_table_sorted | public               | mv_test       | Recompute
      1 | 2020-02-17 22:57:22.497989 | 8455 | ALTER TABLE ALTER DISTSTYLE | dev     | public               | mv_base_table        | public               | mv_test       | Recompute
    173 | 2020-02-17 22:57:23.591434 | 8504 | Table was renamed           | dev     |                      | mv_base_table        | public               | mv_test       | Unrefreshable
    173 | 2020-02-17 22:57:27.229423 | 8592 | Column type was changed     | dev     |                      | mv_base_table        | public               | mv_test       | Unrefreshable
    197 | 2020-02-17 22:59:06.212569 | 9668 | TRUNCATE                    | dev     | schemaf796e415850f4f | mv_base_table        | schemaf796e415850f4f | mv_test       | Recompute
    138 | 2020-02-14 02:21:55.705655 | 5226 | Column was renamed          | dev     |                      | mv_base_table        | public               | mv_test       | Unrefreshable
      1 | 2020-02-14 02:22:26.292434 | 5325 | ALTER TABLE ALTER SORTKEY   | dev     | public               | mv_base_table_sorted | public               | mv_test       | Recompute
```