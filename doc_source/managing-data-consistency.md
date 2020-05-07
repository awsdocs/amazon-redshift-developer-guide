# Managing data consistency<a name="managing-data-consistency"></a>

Amazon S3 provides eventual consistency for some operations, so it is possible that new data will not be available immediately after the upload, which could result in an incomplete data load or loading stale data\. COPY operations where the cluster and the bucket are in different regions are eventually consistent\. All regions provide read\-after\-write consistency for uploads of new objects with unique object keys\. For more information about data consistency, see [Amazon S3 Data Consistency Model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel) in the *Amazon Simple Storage Service Developer Guide*\.

To ensure that your application loads the correct data, we recommend the following practices:
+ Create new object keys\. 

  Amazon S3 provides eventual consistency in all regions for overwrite operations\. Creating new file names, or object keys, in Amazon S3 for each data load operation provides strong consistency in all regions\. 
+ Use a manifest file with your COPY operation\. 

  The manifest explicitly names the files to be loaded\. Using a manifest file enforces strong consistency\.

The rest of this section explains these steps in detail\.

## Creating new object keys<a name="creating-new-keys"></a>

Because of potential data consistency issues, we strongly recommend creating new files with unique Amazon S3 object keys for each data load operation\. If you overwrite existing files with new data, and then issue a COPY command immediately following the upload, it is possible for the COPY operation to begin loading from the old files before all of the new data is available\. For more information about eventual consistency, see [Amazon S3 Data Consistency Model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel) in the *Amazon S3 Developer Guide*\.

## Using a manifest file<a name="using-a-manifest-file"></a>

You can explicitly specify which files to load by using a manifest file\. When you use a manifest file, COPY enforces strong consistency by searching secondary servers if it does not find a listed file on the primary server\. The manifest file can be configured with an optional `mandatory` flag\. If `mandatory` is `true` and the file is not found, COPY returns an error\.

For more information about using a manifest file, see the [copy_from_s3_manifest_file](copy-parameters-data-source-s3.md#copy-manifest-file) option for the COPY command and [Using a manifest to specify data files](r_COPY_command_examples.md#copy-command-examples-manifest) in the COPY examples\. 

Because Amazon S3 provides eventual consistency for overwrites in all regions, it is possible to load stale data if you overwrite existing objects with new data\. As a best practice, never overwrite existing files with new data\.