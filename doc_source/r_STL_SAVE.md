# STL\_SAVE<a name="r_STL_SAVE"></a>

Contains details for *save* steps in queries\. A save step saves the input stream to a transient table\. A transient table is a temporary table that stores intermediate results during query execution\.

A query consists of multiple segments, and each segment consists of one or more steps\. For more information, see [Query processing](c-query-processing.md)\. 

STL\_SAVE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="w31aac53c13c11c91b9"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SAVE.html)

## Sample queries<a name="r_STL_SAVE-sample-queries"></a>

The following query shows which save steps in the most recent query were executed on each slice\. 

```
select query, slice, segment, step, tasknum, rows,  tbl  
from stl_save where query = pg_last_query_id();

 query | slice | segment | step | tasknum | rows | tbl
-------+-------+---------+------+---------+------+-----
 52236 |     3 |       0 |    2 |      21 |    0 | 239
 52236 |     2 |       0 |    2 |      20 |    0 | 239
 52236 |     2 |       2 |    2 |      20 |    0 | 239
 52236 |     3 |       2 |    2 |      21 |    0 | 239
 52236 |     1 |       0 |    2 |      21 |    0 | 239
 52236 |     0 |       0 |    2 |      20 |    0 | 239
 52236 |     0 |       2 |    2 |      20 |    0 | 239
 52236 |     1 |       2 |    2 |      21 |    0 | 239
(8 rows)
```