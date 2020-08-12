# STL\_UNLOAD\_LOG<a name="r_STL_UNLOAD_LOG"></a>

Records the details for an unload operation\.

STL\_UNLOAD\_LOG records one row for each file created by an UNLOAD statement\. For example, if an UNLOAD creates 12 files, STL\_UNLOAD\_LOG will contain 12 corresponding rows\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_UNLOAD_LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_UNLOAD_LOG.html)

## Sample query<a name="r_STL_UNLOAD_LOG-sample-query"></a>

To get a list of the files that were written to Amazon S3 by an UNLOAD command, you can call an Amazon S3 list operation after the UNLOAD completes; however, depending on how quickly you issue the call, the list might be incomplete because an Amazon S3 list operation is eventually consistent\. To get a complete, authoritative list immediately, query STL\_UNLOAD\_LOG\.

The following query returns the pathname for files that were created by an UNLOAD for the last query executed:

```
select query, substring(path,0,40) as path
from stl_unload_log
where query = pg_last_query_id() 
order by path;
```

This command returns the following sample output: 

```
 query |             path
-------+--------------------------------------
  2320 | s3://my-bucket/venue0000_part_00
  2320 | s3://my-bucket/venue0001_part_00
  2320 | s3://my-bucket/venue0002_part_00
  2320 | s3://my-bucket/venue0003_part_00
(4 rows)
```