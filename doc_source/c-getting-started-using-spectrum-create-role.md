# Step 1\. Create an IAM role for Amazon Redshift<a name="c-getting-started-using-spectrum-create-role"></a>

Your cluster needs authorization to access your external Data Catalog in AWS Glue or Amazon Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. For more information about using roles with Amazon Redshift, see [Authorizing COPY and UNLOAD Operations Using IAM Roles](https://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html)\.

**Note**  
In certain cases, you can migrate your Athena Data Catalog to an AWS Glue Data Catalog\. You can do this if your cluster is in an AWS Region where AWS Glue is supported and you have Redshift Spectrum external tables in the Athena Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](https://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

When you create a role for Amazon Redshift, choose one of the following approaches:
+ If you are using Redshift Spectrum with either an Athena Data Catalog or AWS Glue Data Catalog, follow the steps outlined in [To create an IAM role for Amazon Redshift](#spectrum-get-started-create-role)\. 
+ If you are using Redshift Spectrum with an AWS Glue Data Catalog that is enabled for AWS Lake Formation, follow the steps outlined in these procedures:
  +  [To create an IAM role for Amazon Redshift using an AWS Glue Data Catalog enabled for AWS Lake Formation ](#spectrum-get-started-create-role-lake-formation) 
  +  [To grant SELECT permissions on the table to query in the Lake Formation database](#spectrum-get-started-grant-lake-formation-table) <a name="spectrum-get-started-create-role"></a>

**To create an IAM role for Amazon Redshift**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Choose **AWS service**, and then choose **Redshift**\.

1. Under **Select your use case**, choose **Redshift \- Customizable** and then choose **Next: Permissions**\.

1. The **Attach permissions policy** page appears\. Choose `AmazonS3ReadOnlyAccess` and `AWSGlueConsoleFullAccess`, if you're using the AWS Glue Data Catalog\. Or choose `AmazonAthenaFullAccess` if you're using the Athena Data Catalog\. Choose **Next: Review**\.
**Note**  
The `AmazonS3ReadOnlyAccess` policy gives your cluster read\-only access to all Amazon S3 buckets\. To grant access to only the AWS sample data bucket, create a new policy and add the following permissions\.  

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

1. For **Role name**, enter a name for your role, for example **mySpectrumRole**\.

1. Review the information, and then choose **Create role**\.

1. In the navigation pane, choose **Roles**\. Choose the name of your new role to view the summary, and then copy the **Role ARN** to your clipboard\. This value is the Amazon Resource Name \(ARN\) for the role that you just created\. You use that value when you create external tables to reference your data files on Amazon S3\.<a name="spectrum-get-started-create-role-lake-formation"></a>

**To create an IAM role for Amazon Redshift using an AWS Glue Data Catalog enabled for AWS Lake Formation**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

   If this is your first time choosing **Policies**, the **Welcome to Managed Policies** page appears\. Choose **Get Started**\.

1. Choose **Create policy**\. 

1. Choose to create the policy on the **JSON** tab\. 

1. Paste in the following JSON policy document, which grants access to the Data Catalog but denies the administrator permissions for Lake Formation\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "RedshiftPolicyForLF",
               "Effect": "Allow",
               "Action": [
                   "glue:*",
                   "lakeformation:GetDataAccess"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. When you are finished, choose **Review** to review the policy\. The policy validator reports any syntax errors\.

1. On the **Review policy** page, for **Name** enter **mySpectrumPolicy** to name the policy that you are creating\. Enter a **Description** \(optional\)\. Review the policy **Summary** to see the permissions that are granted by your policy\. Then choose **Create policy** to save your work\.

   After you create a policy, you can create a role and apply the policy\. 

1. In the navigation pane of the IAM console, choose **Roles**, and then choose **Create role**\.

1. For **Select type of trusted entity**, choose **AWS service**\.

1. Choose the Amazon Redshift service to assume this role\.

1. Choose the **Redshift Customizable** use case for your service\. Then choose **Next: Permissions**\.

1. Choose the permissions policy that you created, `mySpectrumPolicy`, to attach to the role\.

1. Choose **Next: Tagging**\.

1. Choose **Next: Review**\. 

1. For **Role name**, enter the name **mySpectrumRole**\. 

1. \(Optional\) For **Role description**, enter a description for the new role\.

1. Review the role, and then choose **Create role**\.<a name="spectrum-get-started-grant-lake-formation-table"></a>

**To grant SELECT permissions on the table to query in the Lake Formation database**

1. Open the Lake Formation console at [https://console\.aws\.amazon\.com/lakeformation/](https://console.aws.amazon.com/lakeformation/)\.

1. In the navigation pane, choose **Permissions**, and then choose **Grant**\.

1. Provide the following information:
   + For **IAM role**, choose the IAM role you created, `mySpectrumRole`\. When you run the Amazon Redshift Query Editor, it uses this IAM role for permission to the data\. 
**Note**  
To grant SELECT permission on the table in a Lake Formation–enabled Data Catalog to query, do the following:  
Register the path for the data in Lake Formation\. 
Grant users permission to that path in Lake Formation\. 
Created tables can be found in the path registered in Lake Formation\. 
   + For **Database**, choose your Lake Formation database\. 
   + For **Table**, choose a table within the database to query\. 
   + For **Columns**, choose **All Columns**\.
   + Choose the **Select** permission\.

1. Choose **Save**\.

**Important**  
As a best practice, allow access only to the underlying Amazon S3 objects through Lake Formation permissions\. To prevent unapproved access, remove any permission granted to Amazon S3 objects outside of Lake Formation\. If you previously accessed Amazon S3 objects before setting up Lake Formation, remove any IAM policies or bucket permissions that previously were set up\. For more information, see [Upgrading AWS Glue Data Permissions to the AWS Lake Formation Model](https://docs.aws.amazon.com/lake-formation/latest/dg/upgrade-glue-lake-formation.html) and [Lake Formation Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/lake-formation-permissions.html)\. 