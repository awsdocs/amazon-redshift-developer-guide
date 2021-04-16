# STL\_DISK\_FULL\_DIAG<a name="r_STL_DISK_FULL_DIAG"></a>

Logs information about errors recorded when the disk is full\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_DISK_FULL_DIAG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DISK_FULL_DIAG.html)

## Sample queries<a name="r_STL_DISK_FULL_DIAG-sample-queries"></a>

The following example returns details about the data stored when there is a disk\-full error\. 

```
select * from stl_disk_full_diag
```

The following example converts the `currenttime` to a timestamp\. 

```
select '2000-01-01'::timestamp + (currenttime/1000000.0)* interval '1 second' as currenttime,node_num,query_id,temp_blocks from pg_catalog.stl_disk_full_diag;
```

```
        currenttime         | node_num | query_id | temp_blocks 
----------------------------+----------+----------+-------------
 2019-05-18 19:19:18.609338 |        0 |   569399 |       70982
 2019-05-18 19:37:44.755548 |        0 |   569580 |       70982
 2019-05-20 13:37:20.566916 |        0 |   597424 |       70869
```