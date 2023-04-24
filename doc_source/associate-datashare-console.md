# Associating datashares<a name="associate-datashare-console"></a>

As a consumer cluster administrator, you can associate one or more datashares that are shared from other accounts to your entire AWS account or specific cluster namespaces in your account\.  

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Datashares**\. The datashare list page appears\.

1. Choose **From other accounts**\.

1. In the **Datashares from other accounts** section, choose the datashare that you want to associate and choose **Associate**\. When the Associate datashare page appears, choose one of the following Association types:
   + Choose **Entire AWS account** to associate all existing and future cluster namespaces across different AWS Regions in your AWS account with the datashare\. Then choose **Associate**\.

     If the datashare is published to the AWS Glue Data Catalog, you can only associate the datashare with the entire AWS account\.
   + Choose **Specific AWS Regions and cluster namespaces** to associate one or more AWS Regions and specific cluster namespaces with the datashare\.

     1. Choose **Add Region** to add specific AWS Regions and cluster namespaces to the datashare\. The **Add AWS Region** page appears\.

     1. Choose an **AWS Region**\. 

     1. Do one of the following:
        + Choose **Add all cluster namespaces** to add all existing and future cluster namespaces in this Region to the datashare\.
        + Choose **Add specific cluster namespaces** to add one or more specific cluster namespaces in this Region to the datashare\.
        + Choose one or more cluster namespaces and choose **Add AWS Region**\.

     1. Choose **Associate**\.

 If you're associating the datashare with a Lake Formation account, go to the Lake Formation console to create a database, then define permissions over the database\. For more information, see [ Setting up permissions for Amazon Redshift datashares](https://docs.aws.amazon.com/lake-formation/latest/dg/setup-ds-perms.html) in the AWS Lake Formation Developer Guide\. Once you create a AWS Glue database or a federated database, you can use query editor v2 or any preferred SQL client with your consumer cluster to query the data\. For more information, see [Working with Lake Formation\-managed datashares as a consumer](lake-formation-getting-started-consumer.md)\. 

After the datashare is associated, the datashares become available\.

You can also change datashare association at any time\. When changing association from specific AWS Regions and cluster namespaces to the entire AWS account, Amazon Redshift overwrites the specific Region and cluster namespaces information with AWS account information\. All the AWS Regions and cluster namespaces in the AWS account then have access to the datashare\.

When changing association from specific cluster namespaces to all cluster namespaces in the specified AWS Region, all cluster namespaces in this Region then have access to the datashare\.