# STL\_HASHJOIN<a name="r_STL_HASHJOIN"></a>

Analyzes hash join execution steps for queries\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_HASHJOIN-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_HASHJOIN.html)

## Sample queries<a name="r_STL_HASHJOIN-sample-queries"></a>

The following example returns the number of partitions used in a hash join for query 720\. 

```
select query, slice, tbl, num_parts
from stl_hashjoin
where query=720 limit 10;
```

```
 query | slice | tbl | num_parts
-------+-------+-----+-----------
   720 |     0 | 243 |         1
   720 |     1 | 243 |         1
(2 rows)
```