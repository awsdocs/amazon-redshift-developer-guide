# Compressing your data files<a name="c_best-practices-compress-data-files"></a>

When you want to compress large load files, we recommend that you use gzip, lzop, bzip2, or Zstandard to compress them and split the data into multiple smaller files\.

Specify the GZIP, LZOP, BZIP2, or ZSTD option with the COPY command\. This example loads the TIME table from a pipe\-delimited lzop file\.

```
copy time
from 's3://mybucket/data/timerows.lzo' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
lzop
delimiter '|';
```

There are instances when you don't have to split uncompressed data files\. For more information about splitting your data and examples of using COPY to load data, see [Loading data from Amazon S3](t_Loading-data-from-S3.md)\. 