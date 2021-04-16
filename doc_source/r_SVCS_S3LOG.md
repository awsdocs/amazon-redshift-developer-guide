# SVCS\_S3LOG<a name="r_SVCS_S3LOG"></a>

Use the SVCS\_S3LOG view to get troubleshooting details about Redshift Spectrum queries at the segment level\. One segment can perform one external table scan\. This view is derived from the SVL\_S3LOG system view but doesn't show slice\-level for queries run on a concurrency scaling cluster\. 

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\.

SVCS\_S3LOG is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVL\_S3LOG, see [SVL\_S3LOG](r_SVL_S3LOG.md)\.

## Table columns<a name="r_SVCS_S3LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3LOG.html)

## Sample query<a name="r_SVCS_S3LOG-sample-query"></a>

The following example queries SVCS\_S3LOG for the last query that ran\.

```
select * 
from svcs_s3log 
where query = pg_last_query_id() 
order by query,segment;
```