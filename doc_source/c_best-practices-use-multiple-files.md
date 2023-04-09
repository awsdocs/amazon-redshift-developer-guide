# Loading data files<a name="c_best-practices-use-multiple-files"></a>

Source\-data files come in different formats and use varying compression algorithms\. When loading data with the COPY command, Amazon Redshift loads all of the files referenced by the Amazon S3 bucket prefix\. \(The prefix is a string of characters at the beginning of the object key name\.\) If the prefix refers to multiple files or files that can be split, Amazon Redshift loads the data in parallel, taking advantage of Amazon Redshift’s MPP architecture\. This divides the workload among the nodes in the cluster\. In contrast, when you load data from a file that can't be split, Amazon Redshift is forced to perform a serialized load, which is much slower\. The following sections describe the recommended way to load different file types into Amazon Redshift, depending on their format and compression\. 

## Loading data from files that can be split<a name="c_best-practices-use-multiple-files-split"></a>

The following files can be automatically split when their data is loaded:
+ an uncompressed CSV file
+ a CSV file compressed with BZIP
+ a columnar file \(Parquet/ORC\)

Amazon Redshift automatically splits files 128MB or larger into chunks\. Columnar files, specifically Parquet and ORC, aren't split if they're less than 128MB\. Redshift makes use of slices working in parallel to load the data\. This provides fast load performance\.

## Loading data from files that can't be split<a name="c_best-practices-use-multiple-files-comma"></a>

 File types such as JSON, or CSV, when compressed with other compression algorithms, such as GZIP, aren't automatically split\. For these we recommend manually splitting the data into multiple smaller files that are close in size, from 1 MB to 1 GB after compression\. Additionally, make the number of files a multiple of the number of slices in your cluster\. For more information about how to split your data into multiple files and examples of loading data using COPY, see [Loading data from Amazon S3](https://docs.aws.amazon.com/redshift/latest/dg/t_Loading-data-from-S3.html)\.