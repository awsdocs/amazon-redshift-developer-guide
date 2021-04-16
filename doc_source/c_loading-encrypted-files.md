# Loading encrypted data files from Amazon S3<a name="c_loading-encrypted-files"></a>

You can use the COPY command to load data files that were uploaded to Amazon S3 using server\-side encryption, client\-side encryption, or both\. 

The COPY command supports the following types of Amazon S3 encryption:
+ Server\-side encryption with Amazon S3\-managed keys \(SSE\-S3\)
+ Server\-side encryption with AWS KMS\-managed keys \(SSE\-KMS\)
+ Client\-side encryption using a client\-side symmetric master key

The COPY command doesn't support the following types of Amazon S3 encryption:
+ Server\-side encryption with customer\-provided keys \(SSE\-C\)
+ Client\-side encryption using an AWS KMS\-managed customer master key
+ Client\-side encryption using a customer\-provided asymmetric master key

For more information about Amazon S3 encryption, see [ Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) and [Protecting Data Using Client\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html) in the Amazon Simple Storage Service Developer Guide\.

The [UNLOAD](r_UNLOAD.md) command automatically encrypts files using SSE\-S3\. You can also unload using SSE\-KMS or client\-side encryption with a customer\-managed symmetric key\. For more information, see [Unloading encrypted data files](t_unloading_encrypted_files.md)

The COPY command automatically recognizes and loads files encrypted using SSE\-S3 and SSE\-KMS\. You can load files encrypted using a client\-side symmetric master key by specifying the ENCRYPTED option and providing the key value\. For more information, see [Uploading encrypted data to Amazon S3](t_uploading-encrypted-data.md)\.

To load client\-side encrypted data files, provide the master key value using the MASTER\_SYMMETRIC\_KEY parameter and include the ENCRYPTED option\.

```
copy customer from 's3://mybucket/encrypted/customer' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key '<master_key>' 
encrypted
delimiter '|';
```

To load encrypted data files that are gzip, lzop, or bzip2 compressed, include the GZIP, LZOP, or BZIP2 option along with the master key value and the ENCRYPTED option\.

```
copy customer from 's3://mybucket/encrypted/customer' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key '<master_key>'
encrypted 
delimiter '|' 
gzip;
```