# Sharing data across AWS Regions \(preview\)<a name="across-region"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

When working with the preview, consider the following:
+ Your clusters must be on a cluster version that supports cross\-Region data sharing to use this feature\. 
+ This feature is currently available for test purposes only\. Don't use the feature for production use cases\.
+  The data sharing preview period is expected to run until January 31, 2022\. Unless the preview period is extended, plan to delete your cluster by January 31, 2022\.
+ You can use a cluster in any AWS Regions with Amazon Redshift RA3 instance types to preview the data sharing feature\. For more information, see [RA3 node type availability in AWS Regions](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#ra3-regions) in the *Amazon Redshift Cluster Management Guide*\.
+ For any questions, issues, or feedback related to the preview features during the preview period, email `redshift-datasharing@amazon.com` or open a support case with AWS Support\. 

You can share data for read purposes across Amazon Redshift clusters in AWS Regions\. With cross\-Region data sharing, you can share data across AWS Regions without the need to copy data manually\. You don't have to unload your data into Amazon S3 and copy the data into a new Amazon Redshift cluster or perform cross\-Region snapshot copy\.

With cross\-Region data sharing, you can share data across clusters in the same AWS account, or in different AWS accounts even when the clusters are in different Regions\. When sharing data with Amazon Redshift clusters that are in the same AWS account but different AWS Regions, follow the same workflow as sharing data within an AWS account account\. For more information, see [Sharing data within an AWS account](within-account.md)\.

If clusters sharing data are in different AWS accounts and AWS Regions, you can follow the same workflow as sharing data across AWS accounts and include Region\-level associations on the consumer cluster\. Cross\-Region data sharing supports datashare association with the entire AWS account, the entire AWS Region, or specific cluster namespaces within an AWS Region\. For more information about sharing data across AWS accounts, see [Sharing data across AWS accounts](across-account.md)\.

To share data between different AWS Regions, you pay the cross\-Region data transfer from the producer Region to the consumer Region based on the price of Amazon S3 in addition to the cost of using your Amazon Redshift cluster\. 

To use the datashare, a consumer account administrator can associate the datashare in one of the following three ways\.
+ Association with an entire AWS account spanning all its AWS Regions
+ Association with a specific AWS Region in an AWS account
+ Association with specific cluster namespaces within an AWS Region

When the administrator chooses the entire AWS account, all existing and future cluster namespaces across different AWS Regions in the account have access to the datashares\. A consumer account administrator can also choose specific AWS Regions or cluster namespaces within a Region to grant them access to the datashares\.

**If you are a producer cluster administrator or database owner**, create a datashare, add database objects and data consumers to the datashare, and grant permissions to data consumers\. For more information, see [Producer cluster administrator actions](producer-cluster-admin.md)\.

**If you are a producer account administrator**, authorize datashares using  the Amazon Redshift console and choose the data consumers\. 

**If you are a consumer account administrator** – follow these steps:

To associate one or more datashares that are shared from other accounts to your entire AWS account or specific AWS Regions or cluster namespaces within an AWS Region, use the Amazon Redshift console\. 

You can use the Amazon Redshift console to associate datashares with your entire AWS account or specific AWS Regions or cluster namespaces within an AWS Region\. To do this, sign in to the [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\. Then associate one or more datashares that are shared from other accounts with your entire AWS account, the entire AWS Region, or a specific cluster namespace within an AWS Region\. For more information, see [Associating datashares](associate-datashare-console.md)\.

After the AWS account or specific cluster namespaces are associated, the datashares become available for consumption\. You can also change datashare association at any time\. When changing association from individual cluster namespaces to an AWS account, Amazon Redshift overwrites the cluster namespaces with the AWS account information\. When changing association from an AWS account to specific cluster namespaces, Amazon Redshift overwrites the AWS account information with the cluster namespace information\. When changing association from an entire AWS account to specific AWS Regions and cluster namespaces, Amazon Redshift overwrites the AWS account information with the specific Region and cluster namespace information\.

**If you are a consumer cluster administrator**, you can create local databases that reference to the datashares and grant permissions on databases created from the datashares to users groups in the consumer cluster as needed\. You can also create views on shared objects and create external schemas to refer and assign granular permissions to specific schemas in the consumer database imported on the consumer cluster\. For more information, see [Consumer cluster administrator actions](consumer-cluster-admin.md)\.