# Loading compressed data files from Amazon S3<a name="t_loading-gzip-compressed-data-files-from-S3"></a>

To load data files that are compressed using gzip, lzop, or bzip2, include the corresponding option: GZIP, LZOP, or BZIP2\.

COPY does not support files compressed using the lzop *\-\-filter* option\.

For example, the following command loads from files that were compressing using lzop\.

```
copy customer from 's3://mybucket/customer.lzo' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|' lzop;
```