# SVL\_S3RETRIES<a name="r_SVL_S3RETRIES"></a>

Use the SVL\_S3RETRIES view to get information about why an Amazon Redshift Spectrum query based on Amazon S3 has failed\.

SVL\_S3RETRIES is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVL_S3RETRIES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3RETRIES.html)

## Sample Query<a name="r_SVL_S3RETRIES-sample-query"></a>

The following example retrieves data about failed S3 queries\.

```
SELECT stl_s3retries.query, stl_s3retries.segment, stl_s3retries.node, stl_s3retries.slice, stl_s3retries.eventtime, stl_s3retries.retries, 
stl_s3retries.successful_fetches, stl_s3retries.file_size, btrim((stl_s3retries."location")::text) AS "location", btrim((stl_s3retries.message)::text)
AS message FROM stl_s3retries;
```