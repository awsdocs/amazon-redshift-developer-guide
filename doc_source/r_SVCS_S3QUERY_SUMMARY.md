# SVCS\_S3QUERY\_SUMMARY<a name="r_SVCS_S3QUERY_SUMMARY"></a>

Use the SVCS\_S3QUERY\_SUMMARY view to get a summary of all Redshift Spectrum queries \(S3 queries\) that have been run on the system\. One segment can perform one external table scan\.  

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\.

SVCS\_S3QUERY\_SUMMARY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVL\_S3QUERY, see [SVL\_S3QUERY](r_SVL_S3QUERY.md)\.

## Table columns<a name="r_SVCS_S3QUERY_SUMMARY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3QUERY_SUMMARY.html)

## Sample query<a name="r_SVCS_S3QUERY_SUMMARY-sample-query"></a>

The following example gets the scan step details for the last query run\.

```
select query, segment, elapsed, s3_scanned_rows, s3_scanned_bytes, s3query_returned_rows, s3query_returned_bytes, files 
from svcs_s3query_summary 
where query = pg_last_query_id() 
order by query,segment;
```

```
query | segment | elapsed | s3_scanned_rows | s3_scanned_bytes | s3query_returned_rows | s3query_returned_bytes | files
------+---------+---------+-----------------+------------------+-----------------------+------------------------+------               
 4587 |       2 |   67811 |               0 |                0 |                     0 |                      0 |     0
 4587 |       2 |  591568 |          172462 |         11260097 |                  8513 |                 170260 |     1
 4587 |       2 |  216849 |               0 |                0 |                     0 |                      0 |     0
 4587 |       2 |  216671 |               0 |                0 |                     0 |                      0 |     0
```