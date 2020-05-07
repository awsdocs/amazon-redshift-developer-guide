# STV\_INFLIGHT<a name="r_STV_INFLIGHT"></a>

Use the STV\_INFLIGHT table to determine what queries are currently running on the cluster\. STV\_INFLIGHT does not show leader\-node only queries\. For more information, see [Leader node–only functions](c_SQL_functions_leader_node_only.md)\.

STV\_INFLIGHT is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_INFLIGHT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_INFLIGHT.html)

## Sample queries<a name="r_STV_INFLIGHT-sample-queries"></a>

To view all active queries currently running on the database, type the following query: 

```
select * from stv_inflight;
```

The sample output below shows two queries currently running, including the STV\_INFLIGHT query itself and a query that was run from a script called `avgwait.sql`: 

```
select slice, query, trim(label) querylabel, pid,
starttime, substring(text,1,20) querytext
from stv_inflight;

slice|query|querylabel | pid |        starttime         |      querytext
-----+-----+-----------+-----+--------------------------+--------------------
1011 |  21 |           | 646 |2012-01-26 13:23:15.645503|select slice, query,
1011 |  20 |avgwait.sql| 499 |2012-01-26 13:23:14.159912|select avg(datediff(
(2 rows)
```