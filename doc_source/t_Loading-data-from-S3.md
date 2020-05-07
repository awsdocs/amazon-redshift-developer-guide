# Loading data from Amazon S3<a name="t_Loading-data-from-S3"></a>

**Topics**
+ [Splitting your data into multiple files](t_splitting-data-files.md)
+ [Uploading files to Amazon S3](t_uploading-data-to-S3.md)
+ [Using the COPY command to load from Amazon S3](t_loading-tables-from-s3.md)

The COPY command leverages the Amazon Redshift massively parallel processing \(MPP\) architecture to read and load data in parallel from files in an Amazon S3 bucket\. You can take maximum advantage of parallel processing by splitting your data into multiple files and by setting distribution keys on your tables\. For more information about distribution keys, see [Choosing a data distribution style](t_Distributing_data.md)\. 

Data from the files is loaded into the target table, one line per row\. The fields in the data file are matched to table columns in order, left to right\. Fields in the data files can be fixed\-width or character delimited; the default delimiter is a pipe \(\|\)\. By default, all the table columns are loaded, but you can optionally define a comma\-separated list of columns\. If a table column is not included in the column list specified in the COPY command, it is loaded with a default value\. For more information, see [Loading default column values](c_loading_default_values.md)\.

Follow this general process to load data from Amazon S3:

1. Split your data into multiple files\. 

1. Upload your files to Amazon S3\. 

1. Run a COPY command to load the table\. 

1. Verify that the data was loaded correctly\.

The rest of this section explains these steps in detail\. 