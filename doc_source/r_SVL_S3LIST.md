# SVL\_S3LIST<a name="r_SVL_S3LIST"></a>

Use the SVL\_S3LIST view to get details about Amazon Redshift Spectrum queries at the segment level\.

SVL\_S3LIST is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVL_S3LIST-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3LIST.html)

## Sample Query<a name="r_SVL_S3LIST-sample-query"></a>

The following example queries SVL\_S3LIST for the last query executed\.

```
select * 
from svl_s3list 
where query = pg_last_query_id() 
order by query,segment;
```