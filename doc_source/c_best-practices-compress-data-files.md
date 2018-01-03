# Compress Your Data Files<a name="c_best-practices-compress-data-files"></a>

We strongly recommend that you individually compress your load files using gzip, lzop, or bzip2 when you have large datasets\.

Specify the GZIP, LZOP, or BZIP2 option with the COPY command\. This example loads the TIME table from a pipe\-delimited lzop file\.

```
copy time
from 's3://mybucket/data/timerows.lzo' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
lzop
delimiter '|';
```