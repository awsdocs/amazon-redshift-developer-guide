# SVL\_S3RETRIES<a name="r_SVL_S3RETRIES"></a>

Use the SVL\_S3RETRIES view to get information about why an Amazon Redshift Spectrum query based on Amazon S3 has failed\.

SVL\_S3RETRIES is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_S3RETRIES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_S3RETRIES.html)

## Sample query<a name="r_SVL_S3RETRIES-sample-query"></a>

The following example retrieves data about failed S3 queries\.

```
SELECT svl_s3retries.query, svl_s3retries.segment, svl_s3retries.node, svl_s3retries.slice, svl_s3retries.eventtime, svl_s3retries.retries, 
svl_s3retries.successful_fetches, svl_s3retries.file_size, btrim((svl_s3retries."location")::text) AS "location", btrim((svl_s3retries.message)::text)
AS message FROM svl_s3retries;
```