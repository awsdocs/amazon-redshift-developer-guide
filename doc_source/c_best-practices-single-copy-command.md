# Use a single COPY command to load from multiple files<a name="c_best-practices-single-copy-command"></a>

Amazon Redshift automatically loads in parallel from multiple data files\.

If you use multiple concurrent COPY commands to load one table from multiple files, Amazon Redshift is forced to perform a serialized load\. This type of load is much slower and requires a VACUUM process at the end if the table has a sort column defined\. For more information about using COPY to load data in parallel, see [Loading data from Amazon S3](t_Loading-data-from-S3.md)\.