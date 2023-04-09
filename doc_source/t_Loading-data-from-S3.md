# Loading data from Amazon S3<a name="t_Loading-data-from-S3"></a>

**Topics**
+ [Loading data from compressed and uncompressed files](t_splitting-data-files.md)
+ [Uploading files to Amazon S3](t_uploading-data-to-S3.md)
+ [Using the COPY command to load from Amazon S3](t_loading-tables-from-s3.md)

The COPY command leverages the Amazon Redshift massively parallel processing \(MPP\)   architecture to read and load data in parallel from a file or multiple files in an Amazon S3 bucket\. You can take maximum advantage of parallel processing by splitting your data into multiple files, in cases where the files are compressed\. \(There are exceptions to this rule\. These are detailed in [Loading data files](https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-use-multiple-files.html)\.\) You can also take maximum advantage of parallel processing by setting distribution keys on your tables\. For more information about distribution keys, see [Working with data distribution styles](t_Distributing_data.md)\. 

Data is loaded into the target table, one line per row\. The fields in the data file are matched to table columns in order, left to right\. Fields in the data files can be fixed\-width or character delimited; the default delimiter is a pipe \(\|\)\. By default, all the table columns are loaded, but you can optionally define a comma\-separated list of columns\. If a table column is not included in the column list specified in the COPY command, it is loaded with a default value\. For more information, see [Loading default column values](c_loading_default_values.md)\.