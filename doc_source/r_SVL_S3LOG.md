# SVL\_S3LOG<a name="r_SVL_S3LOG"></a>

Use the SVL\_S3LOG view to get details about Amazon Redshift Spectrum queries at the segment and node slice level\.

SVL\_S3LOG is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_S3LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3LOG.html)

## Sample query<a name="r_SVL_S3LOG-sample-query"></a>

The following example queries SVL\_S3LOG for the last query executed\.

```
select * 
from svl_s3log 
where query = pg_last_query_id() 
order by query,segment,slice;
```