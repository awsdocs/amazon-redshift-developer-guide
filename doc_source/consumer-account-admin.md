# Consumer account administrator actions<a name="consumer-account-admin"></a>

**If you are a consumer account administrator** – follow these steps:

To associate one or more datashares that are shared from other accounts with your entire AWS account or specific cluster namespaces in your account, use the Amazon Redshift console\.

Sign in to the [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\. Then associate one or more datashares that are shared from other accounts with your entire AWS account or specific cluster namespaces in your account\. For more information, see [Associating datashares](associate-datashare-console.md)\.

After the AWS account or specific cluster namespaces are associated, the datashares become available for consumption\. You can also change datashare association at any time\. When changing association from individual cluster namespaces to an AWS account, Amazon Redshift overwrites the cluster namespaces with the AWS account information\. When changing association from an AWS account to specific cluster namespaces, Amazon Redshift overwrites the AWS account information with the cluster namespace information\. All cluster namespaces in the account get access to the data\.