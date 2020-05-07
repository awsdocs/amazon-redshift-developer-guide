# STL\_RETURN<a name="r_STL_RETURN"></a>

Contains details for *return* steps in queries\. A return step returns the results of queries executed on the compute nodes to the leader node\. The leader node then merges the data and returns the results to the requesting client\. For queries executed on the leader node, a return step returns results to the client\.

A query consists of multiple segments, and each segment consists of one or more steps\. For more information, see [Query processing](c-query-processing.md)\. 

STL\_RETURN is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_RETURN-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_RETURN.html)

## Sample queries<a name="r_STL_RETURN-sample-queries"></a>

The following query shows which steps in the most recent query were executed on each slice\. \(Slice 6411 is on the leader node\.\)

```
SELECT query, slice, segment, step, endtime, rows, packets 
from stl_return where query = pg_last_query_id();

 query |  slice | segment | step |          endtime           | rows | packets 
-------+--------+---------+------+----------------------------+------+---------
     4 |      2 |       3 |    2 | 2013-12-27 01:43:21.469043 |    3 |       0
     4 |      3 |       3 |    2 | 2013-12-27 01:43:21.473321 |    0 |       0
     4 |      0 |       3 |    2 | 2013-12-27 01:43:21.469118 |    2 |       0
     4 |      1 |       3 |    2 | 2013-12-27 01:43:21.474196 |    0 |       0
     4 |      4 |       3 |    2 | 2013-12-27 01:43:21.47704  |    2 |       0
     4 |      5 |       3 |    2 | 2013-12-27 01:43:21.478593 |    0 |       0
     4 |    6411|       4 |    1 | 2013-12-27 01:43:21.480755 |    0 |       0
(7 rows)
```