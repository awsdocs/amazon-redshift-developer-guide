# Loading the HLLSKETCH data type<a name="copy-usage_notes-hll"></a>

You can copy HLL sketches only in sparse or dense format supported by Amazon Redshift\. To use the COPY command on HyperLogLog sketches, use the Base64 format for dense HyperLogLog sketches and the JSON format for sparse HyperLogLog sketches\. For more information, see [HyperLogLog functions](hyperloglog-functions.md)\. 

The following example imports data from a CSV file into a table using CREATE TABLE and COPY\. First, the example creates the table `t1` using CREATE TABLE\.

```
CREATE TABLE t1 (sketch hllsketch, a bigint);
```

Then it uses COPY to import data from a CSV file into the table `t1`\. 

```
COPY t1 FROMs3://mybucket/unload/' IAM_ROLE 'arn:aws:iam::0123456789012:role/MyRedshiftRole' NULL AS 'null' CSV;
```