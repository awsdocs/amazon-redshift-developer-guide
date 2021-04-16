# SVL\_S3PARTITION<a name="r_SVL_S3PARTITION"></a>

Use the SVL\_S3PARTITION view to get details about Amazon Redshift Spectrum partitions at the segment and node slice level\.

SVL\_S3PARTITION is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_S3PARTITION-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3PARTITION.html)

## Sample query<a name="r_SVL_S3PARTITION-sample-query"></a>

The following example gets the partition details for the last query executed\.

```
SELECT query, segment,
       MIN(starttime) AS starttime,
       MAX(endtime) AS endtime,
       datediff(ms,MIN(starttime),MAX(endtime)) AS dur_ms,
       MAX(total_partitions) AS total_partitions,
       MAX(qualified_partitions) AS qualified_partitions,
       MAX(assignment) as assignment_type
FROM svl_s3partition
WHERE query=pg_last_query_id()
GROUP BY query, segment
```

```
query | segment |           starttime           |           endtime           | dur_ms| total_partitions | qualified_partitions | assignment_type
------+---------+-------------------------------+-----------------------------+-------+------------------+----------------------+----------------
99232 |       0 | 2018-04-17 22:43:50.201515    | 2018-04-17 22:43:54.674595  |  4473 |       2526       |        334           | p
```