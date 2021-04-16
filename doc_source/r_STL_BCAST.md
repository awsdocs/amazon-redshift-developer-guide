# STL\_BCAST<a name="r_STL_BCAST"></a>

Logs information about network activity during execution of query steps that broadcast data\. Network traffic is captured by numbers of rows, bytes, and packets that are sent over the network during a given step on a given slice\. The duration of the step is the difference between the logged start and end times\.

To identify broadcast steps in a query, look for bcast labels in the SVL\_QUERY\_SUMMARY view or run the EXPLAIN command and then look for step attributes that include bcast\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_BCAST-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_BCAST.html)

### Sample queries<a name="r_STL_BCAST-sample-queries2"></a>

The following example returns broadcast information for the queries where there are one or more packets, and the difference between the start and end of the query was one second or more\. 

```
select query, slice, step, rows, bytes, packets, datediff(seconds, starttime, endtime)
from stl_bcast
where packets>0 and datediff(seconds, starttime, endtime)>0;
```

```
 query | slice | step | rows | bytes | packets | date_diff
-------+-------+------+------+-------+---------+-----------
   453 |     2 |    5 |    1 |   264 |       1 |         1
   798 |     2 |    5 |    1 |   264 |       1 |         1
  1408 |     2 |    5 |    1 |   264 |       1 |         1
  2993 |     0 |    5 |    1 |   264 |       1 |         1
  5045 |     3 |    5 |    1 |   264 |       1 |         1
  8073 |     3 |    5 |    1 |   264 |       1 |         1
  8163 |     3 |    5 |    1 |   264 |       1 |         1
  9212 |     1 |    5 |    1 |   264 |       1 |         1
  9873 |     1 |    5 |    1 |   264 |       1 |         1
(9 rows)
```