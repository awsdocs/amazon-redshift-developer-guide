# SVV\_QUERY\_STATE<a name="r_SVV_QUERY_STATE"></a>

 Use SVV\_QUERY\_STATE to view information about the execution of currently running queries\.

The SVV\_QUERY\_STATE view contains a data subset of the STV\_EXEC\_STATE table\.

SVV\_QUERY\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVV_QUERY_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_QUERY_STATE.html)

## Sample Queries<a name="r_SVV_QUERY_STATE-sample-queries"></a>

 **Determining the Processing Time of a Query by Step** 

The following query shows how long each step of the query with query ID 279 took to execute and how many data rows Amazon Redshift processed: 

```
select query, seg, step, maxtime, avgtime, rows, label
from svv_query_state
where query = 279
order by query, seg, step;
```

This query retrieves the processing information about query 279, as shown in the following sample output: 

```
query |   seg   | step | maxtime | avgtime |  rows   | label
------+---------+------+---------+---------+---------+-------------------
  279 |       3 |    0 | 1658054 | 1645711 | 1405360 | scan
  279 |       3 |    1 | 1658072 | 1645809 |       0 | project
  279 |       3 |    2 | 1658074 | 1645812 | 1405434 | insert
  279 |       3 |    3 | 1658080 | 1645816 | 1405437 | distribute
  279 |       4 |    0 | 1677443 | 1666189 | 1268431 | scan
  279 |       4 |    1 | 1677446 | 1666192 | 1268434 | insert
  279 |       4 |    2 | 1677451 | 1666195 |       0 | aggr
(7 rows)
```

 **Determining If Any Active Queries Are Currently Running on Disk** 

The following query shows if any active queries are currently running on disk: 

```
select query, label, is_diskbased from svv_query_state
where is_diskbased = 't';
```

This sample output shows any active queries currently running on disk: 

```
 query | label        | is_diskbased
-------+--------------+--------------
1025   | hash tbl=142 |      t
(1 row)
```