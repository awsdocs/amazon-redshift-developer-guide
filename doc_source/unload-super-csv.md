# Unloading semistructured data in CSV or text formats<a name="unload-super-csv"></a>

You can unload tables with SUPER data columns to Amazon S3 in a comma\-separated value \(CSV\) or text format\. Using a combination of navigation and unnest clauses, Amazon Redshift unloads hierarchical data in SUPER data format to Amazon S3 in CSV or text formats\. Subsequently, you can create external tables against unloaded data and query them using Redshift Spectrum\. For information on using UNLOAD and the required IAM permissions, see [UNLOAD](r_UNLOAD.md)\. 

The following example unloads data into Amazon S3\.

```
UNLOAD ('SELECT * FROM region_nations')
TO 's3://xxxxxx/'
IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3-Write'
DELIMITER AS '|'
GZIP
ALLOWOVERWRITE;
```

Unlike other data types where a user\-defined string represents a null value, Amazon Redshift exports the SUPER data columns using the JSON format and represents it as *null* as determined by the JSON format\. As a result, SUPER data columns ignore the NULL \[AS\] option used in UNLOAD commands\.