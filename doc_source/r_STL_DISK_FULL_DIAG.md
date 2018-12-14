# STL\_DISK\_FULL\_DIAG<a name="r_STL_DISK_FULL_DIAG"></a>

Logs information about errors recorded when the disk is full\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_DISK_FULL_DIAG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DISK_FULL_DIAG.html)

## Sample Queries<a name="r_STL_DISK_FULL_DIAG-sample-queries"></a>

The following example returns details about the data stored when there is a disk\-full error\. 

```
select * from stl_disk_full_diag
```