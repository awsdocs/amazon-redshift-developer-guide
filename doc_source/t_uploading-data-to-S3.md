# Uploading files to Amazon S3<a name="t_uploading-data-to-S3"></a>

**Topics**
+ [Managing data consistency](managing-data-consistency.md)
+ [Uploading encrypted data to Amazon S3](t_uploading-encrypted-data.md)
+ [Verifying that the correct files are present in your bucket](verifying-that-correct-files-are-present.md)

After splitting your files, you can upload them to your bucket\. You can optionally compress or encrypt the files before you load them\.

Create an Amazon S3 bucket to hold your data files, and then upload the data files to the bucket\. For information about creating buckets and uploading files, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service Developer Guide\.* 

Amazon S3 provides eventual consistency for some operations, so it is possible that new data will not be available immediately after the upload\. For more information see, [Managing data consistency](managing-data-consistency.md)

**Important**  
The Amazon S3 bucket that holds the data files must be created in the same AWS Region as your cluster unless you use the [REGION](copy-parameters-data-source-s3.md#copy-region) option to specify the Region in which the Amazon S3 bucket is located\.

You can create an Amazon S3 bucket in a specific Region either by selecting the Region when you create the bucket by using the Amazon S3 console, or by specifying an endpoint when you create the bucket using the Amazon S3 API or CLI\.

Following the data load, verify that the correct files are present on Amazon S3\.