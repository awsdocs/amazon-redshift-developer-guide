# SVCS\_S3LIST<a name="r_SVCS_S3LIST"></a>

Use the SVCS\_S3LIST view to get details about Amazon Redshift Spectrum queries at the segment level\. One segment can perform one external table scan\. This view is derived from the SVL\_S3LIST system view but doesn't show slice\-level for queries run on a concurrency scaling cluster\. 

**Note**  
System views with the prefix SVCS provide details about queries on both the main and concurrency scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\.

SVCS\_S3LIST is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVL\_S3LIST, see [SVL\_S3LIST](r_SVL_S3LIST.md)\.

## Table columns<a name="r_SVCS_S3LIST-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_S3LIST.html)

## Sample query<a name="r_SVCS_S3LIST-sample-query"></a>

The following example queries SVCS\_S3LIST for the last query executed\.

```
select * 
from svcs_s3list 
where query = pg_last_query_id() 
order by query,segment;
```