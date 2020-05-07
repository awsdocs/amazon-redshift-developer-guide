# Creating data files for queries in Amazon Redshift Spectrum<a name="c-spectrum-data-files"></a>

The data files that you use for queries in Amazon Redshift Spectrum are commonly the same types of files that you use for other applications\. For example, the same types of files are used with Amazon Athena, Amazon EMR, and Amazon QuickSight\. You can query the data in its original format directly from Amazon S3\. To do this, the data files must be in a format that Redshift Spectrum supports and be located in an Amazon S3 bucket that your cluster can access\. 

The Amazon S3 bucket with the data files and the Amazon Redshift cluster must be in the same AWS Region\. For information about supported AWS Regions, see [Amazon Redshift Spectrum Regions](c-using-spectrum.md#c-spectrum-regions)\.

## Data formats for Redshift Spectrum<a name="c-spectrum-data-files-formats"></a>

Redshift Spectrum supports the following structured and semistructured data formats\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-data-files.html)

In the preceding table, the headings indicate the following:
+ **Columnar** – Whether the file format physically stores data in a column\-oriented structure as opposed to a row\-oriented one\.
+ **Supports parallel reads** – Whether the file format supports reading individual blocks within the file\. Reading individual blocks enables the distributed processing of a file across multiple independent Redshift Spectrum requests instead of having to read the full file in a single request\.
+ **Split unit** – For file formats that can be read in parallel, the split unit is the smallest chunk of data that a single Redshift Spectrum request can process\.
**Note**  
Timestamp values in text files must be in the format `yyyy-MM-dd HH:mm:ss.SSSSSS`, as the following timestamp value shows: `2017-05-01 11:30:59.000000`\.

  We recommend using a columnar storage file format, such as Apache Parquet\. With a columnar storage file format, you can minimize data transfer out of Amazon S3 by selecting only the columns that you need\. 

## Compression types for Redshift Spectrum<a name="c-spectrum-data-files-compression"></a>

To reduce storage space, improve performance, and minimize costs, we strongly recommend that you compress your data files\. Redshift Spectrum recognizes file compression types based on the file extension\.

Redshift Spectrum supports the following compression types and extensions\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-data-files.html)

You can apply compression at different levels\. Most commonly, you compress a whole file or compress individual blocks within a file\. 

For Redshift Spectrum to be able to read a file in parallel, the following must be true:
+ The file format supports parallel reads\.
+ The file\-level compression, if any, supports parallel reads\.

It doesn't matter whether the individual split units within a file are compressed using a compression algorithm that can be read in parallel, because each split unit is processed by a single Redshift Spectrum request\. An example of this is Snappy\-compressed Parquet files\. Individual row groups within the Parquet file are compressed using Snappy, but the top\-level structure of the file remains uncompressed\. In this case, the file can be read in parallel because each Redshift Spectrum request can read and process individual row groups from Amazon S3\.

## Encryption for Redshift Spectrum<a name="c-spectrum-data-files-encryption"></a>

Redshift Spectrum transparently decrypts data files that are encrypted using the following encryption options:
+ Server\-side encryption \(SSE\-S3\) using an AES\-256 encryption key managed by Amazon S3\.
+ Server\-side encryption with keys managed by AWS Key Management Service \(SSE\-KMS\)\. 

Redshift Spectrum doesn't support Amazon S3 client\-side encryption\. For more information on server\-side encryption, see [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon Simple Storage Service Developer Guide*\.

Amazon Redshift uses massively parallel processing \(MPP\) to achieve fast execution of complex queries operating on large amounts of data\. Redshift Spectrum extends the same principle to query external data, using multiple Redshift Spectrum instances as needed to scan files\. Place the files in a separate folder for each table\. 

You can optimize your data for parallel processing by doing the following:
+ If your file format or compression doesn't support reading in parallel, break large files into many smaller files\. We recommend using file sizes between 64 MB and 1 GB\.
+ Keep all the files about the same size\. If some files are much larger than others, Redshift Spectrum can't distribute the workload evenly\. 