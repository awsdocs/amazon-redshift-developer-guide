# Step 3: Upload the files to an Amazon S3 bucket<a name="tutorial-loading-data-upload-files"></a>

In this step, you create an Amazon S3 bucket and upload the data files to the bucket\.

## <a name="tutorial-loading-data-to-upload-files"></a>

**To upload the files to an Amazon S3 bucket**

1. Create a bucket in Amazon S3\.

   1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

   1. Click **Create Bucket**\.

   1. In the **Bucket Name** box of the **Create a Bucket** dialog box, type a bucket name\. 

      The bucket name you choose must be unique among all existing bucket names in Amazon S3\. One way to help ensure uniqueness is to prefix your bucket names with the name of your organization\. Bucket names must comply with certain rules\. For more information, go to [Bucket restrictions and limitations](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service User Guide\.* 

   1. Select a Region\. 

      Create the bucket in the same Region as your cluster\. If your cluster is in the US West \(Oregon\) Region, choose **US West \(Oregon\) Region \(us\-west\-2\)**\.

   1. Choose **Create**\. 

      When Amazon S3 successfully creates your bucket, the console displays your empty bucket in the **Buckets** panel\. 

1. Create a folder\.

   1. Choose the name of the new bucket\.

   1. Choose the **Actions** button, and click **Create Folder** in the drop\-down list\.

   1. Name the new folder **load**\.
**Note**  
The bucket that you created is not in a sandbox\. In this exercise, you add objects to a real bucket\. You're charged a nominal amount for the time that you store the objects in the bucket\. For more information about Amazon S3 pricing, go to the [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/) page\.

1. Upload the data files to the new Amazon S3 bucket\.

   1. Choose the name of the data folder\.

   1. In the Upload \- Select Files wizard, choose **Add Files**\.

      Follow the Amazon S3 console instructions to upload all of the files you downloaded and extracted,

   1. Choose **Start Upload**\.
<a name="tutorial-loading-user-credentials"></a>
**User Credentials**  
The Amazon Redshift COPY command must have access to read the file objects in the Amazon S3 bucket\. If you use the same user credentials to create the Amazon S3 bucket and to run the Amazon Redshift COPY command, the COPY command has all necessary permissions\. If you want to use different user credentials, you can grant access by using the Amazon S3 access controls\. The Amazon Redshift COPY command requires at least ListBucket and GetObject permissions to access the file objects in the Amazon S3 bucket\. For more information about controlling access to Amazon S3 resources, go to [Managing access permissions to your Amazon S3 resources](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html)\.

## Next step<a name="tutorial-loading-next-step4"></a>

[Step 4: Create the sample tables](tutorial-loading-data-create-tables.md)