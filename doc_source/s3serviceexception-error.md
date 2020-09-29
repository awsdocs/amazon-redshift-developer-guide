# S3ServiceException errors<a name="s3serviceexception-error"></a>

The most common s3ServiceException errors are caused by an improperly formatted or incorrect credentials string, having your cluster and your bucket in different AWS Regions, and insufficient Amazon S3 privileges\.

The section provides troubleshooting information for each type of error\.

## Invalid credentials string<a name="invalid-credentials-string-error"></a>

If your credentials string was improperly formatted, you will receive the following error message: 

```
ERROR: Invalid credentials. Must be of the format: credentials 
'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>
[;token=<temporary-session-token>]'
```

Verify that the credentials string does not contain any spaces or line breaks, and is enclosed in single quotation marks\. 

## Invalid access key ID<a name="invalid-access-key-id-error"></a>

If your access key id does not exist, you will receive the following error message: 

```
[Amazon](500310) Invalid operation: S3ServiceException:The AWS Access Key Id you provided does not exist in our records.
```

This is often a copy and paste error\. Verify that the access key ID was entered correctly\. Also, if you are using temporary session keys, check that the value for `token` is set\.

## Invalid secret access key<a name="invalid-secret-access-key-error"></a>

If your secret access key is incorrect, you will receive the following error message: 

```
[Amazon](500310) Invalid operation: S3ServiceException:The request signature we calculated does not match the signature you provided. 
Check your key and signing method.,Status 403,Error SignatureDoesNotMatch
```

This is often a copy and paste error\. Verify that the secret access key was entered correctly and that it is the correct key for the access key ID\.

## Bucket is in a different Region<a name="bucket-in-different-region"></a>

The Amazon S3 bucket specified in the COPY command must be in the same AWS Region as the cluster\. If your Amazon S3 bucket and your cluster are in different Regions, you will receive an error similar to the following: 

```
ERROR: S3ServiceException:The bucket you are attempting to access must be addressed using the specified endpoint.
```

You can create an Amazon S3 bucket in a specific Region either by selecting the Region when you create the bucket by using the Amazon S3 Management Console, or by specifying an endpoint when you create the bucket using the Amazon S3 API or CLI\. For more information, see [Uploading files to Amazon S3](t_uploading-data-to-S3.md)\.

For more information about Amazon S3 regions, see [Accessing a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html#access-bucket-intro) in the *Amazon Simple Storage Service Developer Guide*\.

Alternatively, you can specify the Region using the [REGION](copy-parameters-data-source-s3.md#copy-region) option with the COPY command\.

## Access denied<a name="s3-access-denied-error"></a>

The user account identified by the credentials must have LIST and GET access to the Amazon S3 bucket\. If the user does not have sufficient privileges, you will receive the following error message:

```
ERROR: S3ServiceException:Access Denied,Status 403,Error AccessDenied
```

For information about managing user access to buckets, see [Access Control](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingAuthAccess.html) in the *Amazon S3 Developer Guide*\.