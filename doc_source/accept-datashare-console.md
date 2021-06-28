# Associating datashares \(preview\)<a name="accept-datashare-console"></a>

As a consumer cluster administrator, you can associate one or more datashares that are shared from other accounts to your entire AWS account \(preview\) or specific cluster namespaces in your account\. 

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **DATASHARES**\. The datashare list page appears\.

1. Choose **From other accounts**\.

1. In the **Datashares from other accounts** section, choose the datashare you want to accept\. When the Accept datashare page appears, do one of the following:
   + Choose **Associate the entire AWS account** to associate all cluster namespaces in your AWS account to the datashare\. 
   + Choose **Choose specific cluster namespaces** to associate one or more specific cluster namespaces to the datashare\. Then choose one ore more **Cluster namespace ID**\.

1. Choose **Associate**\. 

After the AWS account \(preview\) or specific cluster namespaces are associated, the datashares become available\.

You can also change datashare association at any time\. When changing association from individual cluster namespaces to an AWS account, Amazon Redshift overwrites the cluster namespaces with the AWS account information\. When changing association from an AWS account to specific cluster namespaces, Amazon Redshift overwrites the AWS account information with the cluster namespace information \(preview\)\.