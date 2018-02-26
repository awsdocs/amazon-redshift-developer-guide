# SVL\_S3PARTITION<a name="r_SVL_S3PARTITION"></a>

Use the SVL\_S3PARTITION view to get details about Amazon Redshift Spectrum partitions at the segment and node slice level\.

SVL\_S3PARTITION is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVL_S3PARTITION-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3PARTITION.html)

## Sample Query<a name="r_SVL_S3PARTITION-sample-query"></a>

The following example gets the partition details for the last query executed\.

```
select query, segment, node, slice, duration,
assigned_partitions as assigned, 
qualified_partitions as qualified, assignment 
from svl_s3partition 
where query = pg_last_query_id() 
order by query,segment,slice;
```

```
query | segment | node | slice | duration | assigned | qualified | assignment
------+---------+------+-------+----------+----------+----------+-----------
17223 |       2 |    0 |     0 |   302671 |       12 |        1 | f         
17223 |       2 |    1 |     2 |   265677 |       12 |        1 | f
```