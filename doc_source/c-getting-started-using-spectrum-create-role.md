# Step 1\. Create an IAM Role for Amazon Redshift<a name="c-getting-started-using-spectrum-create-role"></a>

Your cluster needs authorization to access your external data catalog in AWS Glue or Amazon Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. For more information about using roles with Amazon Redshift, see [Authorizing COPY and UNLOAD Operations Using IAM Roles](http://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html)\.

**Note**  
<<<<<<< HEAD
If your cluster is in a region where AWS Glue is supported and you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.<a name="spectrum-get-started-create-role"></a>
=======
If your cluster is in a region where AWS Glue is supported and you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d

**To create an IAM role for Amazon Redshift**

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Choose **AWS service**, and then choose **Redshift**\.

1. Under **Select your use case**, choose **Redshift \- Customizable** and then choose **Next: Permissions**\.

1. The **Attach permissions policy** page appears\. Choose **AmazonS3ReadOnlyAccess** and **AWSGlueConsoleFullAccess**, if you're using the AWS Glue data catalog, or **AmazonAthenaFullAccess** if you're using the Athena data catalog\. Choose **Next: Review**\.
**Note**  
The AmazonS3ReadOnlyAccess policy gives your cluster read\-only access to all Amazon S3 buckets\. To grant access to only the AWS sample data bucket, create a new policy and add the following permissions\.  

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "s3:Get*",
                   "s3:List*"
               ],
               "Resource": "arn:aws:s3:::awssampledbuswest2/*"
           }
       ]
   }
   ```

1. For **Role name**, type a name for your role, for example **mySpectrumRole**\.

1. Review the information, and then choose **Create role**\.

1. In the navigation pane, choose **Roles**\. Choose the name of your new role to view the summary, and then copy the **Role ARN** to your clipboard\. This value is the Amazon Resource Name \(ARN\) for the role that you just created\. You use that value when you create external tables to reference your data files on Amazon S3\.