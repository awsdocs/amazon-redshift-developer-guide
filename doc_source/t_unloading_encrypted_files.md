# Unloading encrypted data files<a name="t_unloading_encrypted_files"></a>

UNLOAD automatically creates files using Amazon S3 server\-side encryption with AWS\-managed encryption keys \(SSE\-S3\)\. You can also specify server\-side encryption with an AWS Key Management Service key \(SSE\-KMS\) or client\-side encryption with a customer\-managed key \(CSE\-CMK\)\. UNLOAD doesn't support Amazon S3 server\-side encryption using a customer\-supplied key \(SSE\-C\)\. For more information, see [ Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)\. 

To unload to Amazon S3 using server\-side encryption with an AWS KMS key, use the KMS\_KEY\_ID parameter to provide the key ID as shown in the following example\.

```
unload ('select venuename, venuecity from venue')
to 's3://mybucket/encrypted/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
KMS_KEY_ID '1234abcd-12ab-34cd-56ef-1234567890ab'
encrypted;
```

If you want to provide your own encryption key, you can create client\-side encrypted data files in Amazon S3 by using the UNLOAD command with the ENCRYPTED option\. UNLOAD uses the same envelope encryption process that Amazon S3 client\-side encryption uses\. You can then use the COPY command with the ENCRYPTED option to load the encrypted files\.

The process works like this:

1. You create a base64 encoded 256\-bit AES key that you will use as your private encryption key, or *master symmetric key*\. 

1. You issue an UNLOAD command that includes your master symmetric key and the ENCRYPTED option\. 

1. UNLOAD generates a one\-time\-use symmetric key \(called the *envelope symmetric key*\) and an initialization vector \(IV\), which it uses to encrypt your data\. 

1. UNLOAD encrypts the envelope symmetric key using your master symmetric key\. 

1. UNLOAD then stores the encrypted data files in Amazon S3 and stores the encrypted envelope key and IV as object metadata with each file\. The encrypted envelope key is stored as object metadata `x-amz-meta-x-amz-key` and the IV is stored as object metadata `x-amz-meta-x-amz-iv`\. 

For more information about the envelope encryption process, see the [Client\-side data encryption with the AWS SDK for Java and amazon S3](https://aws.amazon.com/articles/2850096021478074) article\. 

To unload encrypted data files, add the master key value to the credentials string and include the ENCRYPTED option\. If you use the MANIFEST option, the manifest file is also encrypted\.

```
unload ('select venuename, venuecity from venue')
to 's3://mybucket/encrypted/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key '<master_key>' 
manifest
encrypted;
```

To unload encrypted data files that are GZIP compressed, include the GZIP option along with the master key value and the ENCRYPTED option\.

```
unload ('select venuename, venuecity from venue')
to 's3://mybucket/encrypted/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key '<master_key>' 
encrypted gzip;
```

To load the encrypted data files, add the MASTER\_SYMMETRIC\_KEY parameter with the same master key value and include the ENCRYPTED option\. 

```
copy venue from 's3://mybucket/encrypted/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key '<master_key>' 
encrypted;
```