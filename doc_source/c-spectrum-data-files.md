# Creating Data Files for Queries in Amazon Redshift Spectrum<a name="c-spectrum-data-files"></a>

The data files that you use for queries in Amazon Redshift Spectrum are commonly the same types of files that you use for other applications such as Amazon Athena, Amazon EMR, and Amazon QuickSight\. If the files are formatted in a format that Redshift Spectrum supports and located in an Amazon S3 bucket that your cluster can access, you can query the data in its original format directly from Amazon S3\.

The Amazon S3 bucket with the data files and the Amazon Redshift cluster must be in the same AWS Region\. For information about supported AWS Regions, see [Amazon Redshift Spectrum Regions](c-using-spectrum.md#c-spectrum-regions)\.

Redshift Spectrum supports the following structured and semistructured data formats:
+ AVRO
+ PARQUET
+ TEXTFILE
+ SEQUENCEFILE
+ RCFILE
+ RegexSerDe
+ Optimized row columnar \(ORC\)
+ Grok
+ OpenCSV 
+ Ion 
+ JSON 

**Note**  
Timestamp values in text files must be in the format `yyyy-MM-dd HH:mm:ss.SSSSSS`, as the following timestamp value shows: `2017-05-01 11:30:59.000000`\.

We recommend using a columnar storage file format, such as Parquet\. With a columnar storage file format, you can minimize data transfer out of Amazon S3 by selecting only the columns you need\. 

**Compression**

To reduce storage space, improve performance, and minimize costs, we strongly recommend compressing your data files\. Redshift Spectrum recognizes file compression types based on the file extension\.

Redshift Spectrum supports the following compression types and extensions:
+ gzip – `.gz`
+ Snappy – `.snappy`
+ bzip2 – `.bz2`

Redshift Spectrum transparently decrypts data files that are encrypted using the following encryption options:
+ Server\-side encryption \(SSE\-S3\) using an AES\-256 encryption key managed by Amazon S3\.
+ Server\-side encryption with keys managed by AWS Key Management Service \(SSE\-KMS\)\. 

Redshift Spectrum doesn't support Amazon S3 client\-side encryption\. For more information, see [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)\.

Amazon Redshift uses massively parallel processing \(MPP\) to achieve fast execution of complex queries operating on large amounts of data\. Redshift Spectrum extends the same principle to query external data, using multiple Redshift Spectrum instances as needed to scan files\. Place the files in a separate folder for each table\. 

You can optimize your data for parallel processing by the following practices:
+ Break large files into many smaller files\. We recommend using file sizes of 64 MB or larger\. Store files for a table in the same folder\.
+ Keep all the files about the same size\. If some files are much larger than others, Redshift Spectrum can't distribute the workload evenly\. 