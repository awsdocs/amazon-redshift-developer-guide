# Unloading semistructured data in the Parquet format<a name="unload-super-parquet"></a>

You can unload tables with SUPER data columns to Amazon S3 in the Parquet format\. Amazon Redshift represents SUPER columns in Parquet as the JSON data type\. This enables semistructured data to be represented in Parquet\. You can query these columns using Redshift Spectrum or ingest them back to Amazon Redshift using the COPY command\. For information on using UNLOAD and the required IAM permissions, see [UNLOAD](r_UNLOAD.md)\. 

The following example unloads data into Amazon S3 in the Parquet format\.

```
UNLOAD ('SELECT * FROM region_nations')
TO 's3://xxxxxx/'
IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3-Write'
FORMAT PARQUET;
```