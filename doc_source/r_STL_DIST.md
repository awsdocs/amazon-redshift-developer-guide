# STL\_DIST<a name="r_STL_DIST"></a>

Logs information about network activity during execution of query steps that distribute data\. Network traffic is captured by numbers of rows, bytes, and packets that are sent over the network during a given step on a given slice\. The duration of the step is the difference between the logged start and end times\.

To identify distribution steps in a query, look for dist labels in the QUERY\_SUMMARY view or run the EXPLAIN command and then look for step attributes that include dist\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_DIST-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DIST.html)

## Sample queries<a name="r_STL_DIST-sample-queries"></a>

The following example returns distribution information for queries with one or more packets and duration greater than zero\. 

```
select query, slice, step, rows, bytes, packets, 
datediff(seconds, starttime, endtime) as duration
from stl_dist
where packets>0 and datediff(seconds, starttime, endtime)>0
order by query
limit 10;
```

```
 query  | slice | step |  rows  |  bytes  | packets | duration
--------+-------+------+--------+---------+---------+-----------
    567 |     1 |    4 |  49990 | 6249564 |     707 |         1
    630 |     0 |    5 |   8798 |  408404 |      46 |         2
    645 |     1 |    4 |   8798 |  408404 |      46 |         1
    651 |     1 |    5 | 192497 | 9226320 |    1039 |         6
    669 |     1 |    4 | 192497 | 9226320 |    1039 |         4
    675 |     1 |    5 |   3766 |  194656 |      22 |         1
    696 |     0 |    4 |   3766 |  194656 |      22 |         1
    705 |     0 |    4 |    930 |   44400 |       5 |         1
 111525 |     0 |    3 |     68 |   17408 |       2 |         1
(9 rows)
```