# STL\_S3CLIENT<a name="r_STL_S3CLIENT"></a>

Records transfer time and other performance metrics\.

Use the STL\_S3CLIENT table to find the time spent transferring data from Amazon S3 as part of a COPY command\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_S3CLIENT-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_S3CLIENT.html)

## Sample Query<a name="r_STL_S3CLIENT-sample-query2"></a>

The following query returns the time taken to load files using a COPY command\.

```
select slice, key, transfer_time 
from stl_s3client 
where query = pg_last_copy_id();
```

Result

```
 slice |   key                       | transfer_time
 ------+-----------------------------+---------------
     0 | listing10M0003_part_00      |    16626716
     1 | listing10M0001_part_00      |    12894494
     2 | listing10M0002_part_00      |    14320978
     3 | listing10M0000_part_00      |    11293439
  3371 | prefix=listing10M;marker=   |       99395
```