# SVCS\_S3PARTITION\_SUMMARY<a name="r_SVCS_S3PARTITION_SUMMARY"></a>

Use the SVCS\_S3PARTITION\_SUMMARY view to get a summary of Redshift Spectrum queries partition processing at the segment level\. One segment can perform one external table scan\.

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\.

SVCS\_S3PARTITION\_SUMMARY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVL\_S3PARTITION, see [SVL\_S3PARTITION](r_SVL_S3PARTITION.md)\.

## Table columns<a name="r_SVCS_S3PARTITION_SUMMARY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3PARTITION_SUMMARY.html)

## Sample query<a name="r_SVCS_S3PARTITION_SUMMARY-sample-query"></a>

The following example gets the partition scan details for the last query executed\.

```
select query, segment, assignment, min_starttime, max_endtime, min_dureation, avg_duration 
from svcs_s3partition_summary 
where query = pg_last_query_id() 
order by query,segment;
```