# SYS\_UNLOAD\_DETAIL<a name="SYS_UNLOAD_DETAIL"></a>

Use SYS\_UNLOAD\_DETAIL to view details of an UNLOAD operation\. It records one row for each file created by an UNLOAD statement\. For example, if an UNLOAD creates 12 files, SYS\_UNLOAD\_DETAIL will contain 12 corresponding rows\.

SYS\_UNLOAD\_DETAIL is visible to all users\. Superusers can see all rows; regular users can see only their own data\.

## Table columns<a name="SYS_UNLOAD_DETAIL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_UNLOAD_DETAIL.html)

## Sample queries<a name="SYS_UNLOAD_DETAIL-sample-queries"></a>

The following query shows the unloaded query details, including format, rows and file count of unload command\.

```
select query_id, substring(file_name, 0, 50), transfer_size, file_format from sys_unload_detail;
```

Sample output\.

```
 query_id |                     substring                     | transfer_size | file_format 
----------+---------------------------------------------------+---------------+-------------
     9272 | s3://my-bucket/my_unload_doc_venue0000_part_00.gz |        395886 | Text      
     9272 | s3://my-bucket/my_unload_doc_venue0001_part_00.gz |        406444 | Text      
     9272 | s3://my-bucket/my_unload_doc_venue0002_part_00.gz |        409431 | Text      
     9272 | s3://my-bucket/my_unload_doc_venue0003_part_00.gz |        403051 | Text      
     9272 | s3://my-bucket/my_unload_doc_venue0004_part_00.gz |        413592 | Text      
     9272 | s3://my-bucket/my_unload_doc_venue0005_part_00.gz |        395689 | Text      
(6 rows)
```