# SVCS\_COMPILE<a name="r_SVCS_COMPILE"></a>

Records compile time and location for each query segment of queries, including queries run on a scaling cluster as well as queries run on the main cluster\.

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\.

SVCS\_COMPILE is visible to all users\.

For information about SCL\_COMPILE, see [SVL\_COMPILE](r_SVL_COMPILE.md)\.

## Table columns<a name="r_SVCS_COMPILE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_COMPILE.html)

## Sample queries<a name="r_SVCS_COMPILE-sample-queries"></a>

In this example, queries 35878 and 35879 executed the same SQL statement\. The compile column for query 35878 shows `1` for four query segments, which indicates that the segments were compiled\. Query 35879 shows `0` in the compile column for every segment, indicating that the segments did not need to be compiled again\.

```
select userid, xid,  pid, query, segment, locus,  
datediff(ms, starttime, endtime) as duration, compile 
from svcs_compile 
where query = 35878 or query = 35879
order by query, segment;

 userid |  xid   |  pid  | query | segment | locus | duration | compile
--------+--------+-------+-------+---------+-------+----------+---------
    100 | 112780 | 23028 | 35878 |       0 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       1 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       2 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       3 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       4 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       5 |     1 |        0 |       0
    100 | 112780 | 23028 | 35878 |       6 |     1 |     1380 |       1
    100 | 112780 | 23028 | 35878 |       7 |     1 |     1085 |       1
    100 | 112780 | 23028 | 35878 |       8 |     1 |     1197 |       1
    100 | 112780 | 23028 | 35878 |       9 |     2 |      905 |       1
    100 | 112782 | 23028 | 35879 |       0 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       1 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       2 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       3 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       4 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       5 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       6 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       7 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       8 |     1 |        0 |       0
    100 | 112782 | 23028 | 35879 |       9 |     2 |        0 |       0
(20 rows)
```