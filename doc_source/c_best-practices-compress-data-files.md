# Compress your data files<a name="c_best-practices-compress-data-files"></a>

We strongly recommend that you individually compress your load files using gzip, lzop, bzip2, or Zstandard when you have large datasets\.

Specify the GZIP, LZOP, BZIP2, or ZSTD option with the COPY command\. This example loads the TIME table from a pipe\-delimited lzop file\.

```
copy time
from 's3://mybucket/data/timerows.lzo' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
lzop
delimiter '|';
```