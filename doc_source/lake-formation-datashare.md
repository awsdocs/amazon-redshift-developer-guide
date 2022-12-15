# AWS Lake Formation\-managed Redshift datashares \(preview\)<a name="lake-formation-datashare"></a>


|  | 
| --- |
| This is prerelease documentation for the Lake Formation\-managed datashares feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. Public preview will end on February 28, 2023\. Preview clusters and preview serverless workgroups and namespaces will be removed automatically two weeks after the end of the preview\. For preview terms and conditions, see Betas and Previews in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

**Note**  
You can create an Amazon Redshift cluster in **Preview** to test new features of Amazon Redshift\. You can't use those features in production or move your **Preview** cluster to a production cluster or a cluster on another track\. For preview terms and conditions, see *Beta and Previews* in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.  
Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
On the navigation menu, choose **Provisioned clusters dashboard**, and choose **Clusters**\. The clusters for your account in the current AWS Region are listed\. A subset of properties of each cluster is displayed in columns in the list\.
A banner displays on the **Clusters** list page that introduces preview\. Choose the button **Create preview cluster** to open the create cluster page\.
Enter properties for your cluster\. Choose the **Preview track** that contains the features you want to test\. We recommend entering a name for the cluster that indicates that it is on a preview track\. Choose options for your cluster, including options labeled as **\-preview**, for the features you want to test\. For general information about creating clusters, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the *Amazon Redshift Management Guide*\.
Choose **Create preview cluster** to create a cluster in preview\.
When your preview cluster is available, use your SQL client to load and query data\.

When working with the Lake Formation managed Redshift datashares preview, consider the following:
+ You must create new Amazon Redshift clusters with the preview\_2022 maintenance track\. For more information about preview tracks, see [Choosing cluster maintenance tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks)\.
+ This feature is currently available for test purposes only\. Don’t use the feature for production use cases\.
+ You can’t switch an existing Amazon Redshift cluster from the current or trailing state to this preview track, or from the preview track to the current or trailing state\. However, you can restore data from a cluster snapshot running on the current or trailing track\.
+ You can use a cluster with Amazon Redshift RA3 instance types to preview this feature\. For more information, see RA3 node type availability in [AWS Regions](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#ra3-regions) in the *Amazon Redshift Management Guide*\.
+ For any questions, issues, or feedback related to the preview features during the preview period, email redshift\-datasharing@amazon\.com or open a support case with AWS support\.

This preview is currently available in the following regions:
+ ap\-northeast\-1
+ eu\-north\-1
+ eu\-west\-1
+ us\-east\-1
+ us\-east\-2
+ us\-west\-2

 Using AWS Lake Formation, you can centrally define and enforce database, table, column, and row\-level access permissions of Amazon Redshift datashares and restrict user access to objects within a datashare\. For example, you might create and manage row\-level security policies on each consumer cluster to restrict access of a database to certain users\. By sharing data through Lake Formation, you can define permissions in Lake Formation and apply those permissions to any datashare and its objects\. For example, if you have a table containing employee information, you can use Lake Formation's column\-level filters to prevent employees who don't work in the HR department from seeing personally identifiable \(PII\), such as a social security number\. Redshift currently doesn't support Lake Formation's cell\-level filters\. For more information about data filters, see [Data filtering and cell\-level security in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering.html) in the *AWS Lake Formation Developer Guide*\. 

 Amazon Redshift currently supports data sharing via Lake Formation when sharing within the same account or across accounts\. Cross\-Region sharing is currently not supported\. 

The following is a high\-level overview of how to use Lake Formation to control datashare permissions:

1. In Amazon Redshift, the producer cluster administrator creates a datashare on the producer cluster and grants usage to a Lake Formation account\.

1. The producer cluster administrator authorizes the Lake Formation account to access the datashare\.

1. The Lake Formation administrator discovers the datashares they have been granted usage to\.

1. The Lake Formation administrator creates a AWS Glue database in the AWS Glue Data Catalog, and configures Lake Formation permissions to control user access to objects within the datashare\. This database can be a standard [AWS Glue database](https://docs.aws.amazon.com/glue/latest/dg/define-database.html) or a federated database\. For more information about federated databases in AWS Glue, see [Setting up permissions for Amazon Redshift datashares ](https://docs.aws.amazon.com/lake-formation/latest/dg/setup-ds-perms.html)\.

1. A Redshift consumer cluster administrator discovers the AWS Glue databases they have access to and associates the datashare with an AWS Glue Data Catalog ARN\.

1. The consumer cluster administrator discovers the AWS Glue database ARNs they have access to and creates a local database in the consumer cluster using a AWS Glue database ARN\.

1. The consumer cluster administrator grants permissions to users and groups within the consumer cluster, so they have querying permissions\.

1. Database users can use the views SVV\_EXTERNAL\_TABLES and SVV\_EXTERNAL\_COLUMNS to find all of the tables or columns within the AWS Glue database that they have access to, and then they can query the AWS Glue database’s tables\.

1. When the producer cluster administrator decides to no longer share the data with the consumer cluster, the producer cluster administrator can disassociate or delete the datashare from Redshift\. The associated permissions and objects in Lake Formation are not automatically deleted\.

For more information about sharing a datashare with AWS Lake Formation as a producer cluster administrator, see [Working with Lake Formation\-managed datashares as a producer](lake-formation-getting-started-producer.md)\. To consume the shared data from the producer cluster, see [Working with Lake Formation\-managed datashares as a consumer](lake-formation-getting-started-consumer.md)\.

## Considerations and limitations when using AWS Lake Formation with Amazon Redshift<a name="lake-formation-datashare-considerations"></a>

The following are considerations for sharing Amazon Redshift data via Lake Formation\. For information on data sharing limitations, see [Limitations for data sharing](https://docs.aws.amazon.com/redshift/latest/dg/limitations-datashare.html)\.
+ Sharing a datashare to Lake Formation across Regions is currently unsupported\.
+ Row\-level filters are unsupported for views\.
+ Column\-level filters are supported for both tables and views\.
+ Cell\-level filters are unsupported\.
+ If you created and shared a view and its tables to Lake Formation, you can configure filters to manage access of the tables, but policies are applied to objects only if you access them directly\. Amazon Redshift does not apply security policies on objects if you access them through a shared view\.
+ You can't create materialized views based on a table if the table has Lake Formation filters configured\.
+ The Lake Formation administrator must have [ data lake administrator](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#create-data-lake-admin) permissions\.
+ The producer consumer cluster must use the RA3 instance types with the latest Amazon Redshift cluster version to share datashares via Lake Formation\.
+ Both the producer and consumer clusters must be encrypted\.
+ Redshift row\-level and column\-level access control policies implemented in the producer cluster are ignored when the datashare is shared to Lake Formation\. The Lake Formation administrator must configure these policies in Lake Formation\.
+ Sharing datashares via Lake Formation is only available to IAM users who have access to both Redshift; and Lake Formation\.