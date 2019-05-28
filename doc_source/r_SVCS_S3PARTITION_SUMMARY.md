# SVCS\_S3PARTITION\_SUMMARY<a name="r_SVCS_S3PARTITION_SUMMARY"></a>

Use the SVCS\_S3PARTITION\_SUMMARY view to get a summary of Redshift Spectrum queries partition processing at the segment level\. One segment can perform one external table scan\.

SVCS\_S3PARTITION\_SUMMARY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVCS_S3PARTITION_SUMMARY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3PARTITION_SUMMARY.html)

## Sample Query<a name="r_SVCS_S3PARTITION_SUMMARY-sample-query"></a>

The following example gets the partition scan details for the last query executed\.

```
select query, segment, assignment, min_starttime, max_endtime, min_dureation, avg_duration 
from svcs_s3partition_summary 
where query = pg_last_query_id() 
order by query,segment;
```