# Sharing licensed Amazon Redshift data on AWS Data Exchange \(preview\)<a name="adx-getting-started"></a>


|  | 
| --- |
| This is prerelease documentation for the AWS Data Exchange for Amazon Redshift feature, which is in preview release\. The documentation and the feature are both subject to change\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to adx\-redshift\-preview@amazon\.com\.   | 

When working with the preview, consider the following:
+ For producers, your data must be in an encrypted RA3 instance type that has the latest Amazon Redshift cluster version\.
+ You can use a cluster in AWS Regions where AWS Data Exchange is offered with Amazon Redshift RA3 instance types to preview the feature\. For more information, see [RA3 node type availability in AWS Regions](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#ra3-regions) in the *Amazon Redshift Cluster Management Guide*\.
+ For any questions, issues, or feedback related to the preview feature during the preview period, email `adx-redshift-preview@amazon.com` or open a support case with AWS Support\. 

When creating AWS Data Exchange datashares and adding them to an AWS Data Exchange product, providers can license data in Amazon Redshift that consumers can discover, subscribe to, and query up\-to\-date data in Amazon Redshift when they have active AWS Data Exchange subscriptions\.

With AWS Data Exchange datashares added to an AWS Data Exchange product, consumers automatically have access to a product's datashares when their subscription starts and retain their access as long as their subscription is active\.