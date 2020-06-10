# SVCS\_UNLOAD\_LOG<a name="r_SVCS_UNLOAD_LOG"></a>

Use the SVCS\_UNLOAD\_LOG to get details of UNLOAD operations\.

SVCS\_UNLOAD\_LOG records one row for each file created by an UNLOAD statement\. For example, if an UNLOAD creates 12 files, SVCS\_UNLOAD\_LOG contains 12 corresponding rows\. This view is derived from the STL\_UNLOAD\_LOG system table but doesn't show slice\-level for queries run on a concurrency scaling cluster\. 

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the tables with the prefix STL except that the STL tables provide information only for queries run on the main cluster\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVCS_UNLOAD_LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_UNLOAD_LOG.html)

## Sample query<a name="r_SVCS_UNLOAD_LOG-sample-query"></a>

To get a list of the files that were written to Amazon S3 by an UNLOAD command, you can call an Amazon S3 list operation after the UNLOAD completes; however, depending on how quickly you issue the call, the list might be incomplete because an Amazon S3 list operation is eventually consistent\. To get a complete, authoritative list immediately, query SVCS\_UNLOAD\_LOG\.

The following query returns the path name for files that were created by an UNLOAD with for the last query executed:

```
select query, substring(path,0,40) as path
from svcs_unload_log
where query = pg_last_query_id()
order by path;
```

This command returns the following sample output: 

```
 query |             path
 ------+---------------------------------
  2320 | s3://my-bucket/venue0000_part_00
  2320 | s3://my-bucket/venue0001_part_00
  2320 | s3://my-bucket/venue0002_part_00
  2320 | s3://my-bucket/venue0003_part_00
(4 rows)
```