# SVCS\_S3LOG<a name="r_SVCS_S3LOG"></a>

Use the SVCS\_S3LOG view to get troubleshooting details about Redshift Spectrum queries at the segment level\. One segment can perform one external table scan\.

SVCS\_S3LOG is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVCS_S3LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3LOG.html)

## Sample Query<a name="r_SVCS_S3LOG-sample-query"></a>

The following example queries SVCS\_S3LOG for the last query that ran\.

```
select * 
from svcs_s3log 
where query = pg_last_query_id() 
order by query,segment;
```