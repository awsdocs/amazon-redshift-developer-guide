# Integrating Amazon Redshift data sharing with AWS CloudTrail<a name="cloudtrail"></a>

Data sharing is integrated with AWS CloudTrail\. CloudTrail is a service that provides a record of actions taken by a user, a role, or an AWS service in Amazon Redshift\. CloudTrail captures all API calls for data sharing as events\. The calls captured include calls from the Amazon Redshift console and code calls to the data sharing operations\. 

When you create a CloudTrail trail, you can turn on continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for data sharing\. When you don't configure a trail, you can still view the most recent events in the CloudTrail console under **Event history**\. Using the information collected by CloudTrail, you can get certain information\. This information includes the request that was made to Amazon Redshift, the IP address from which the request was made, who made the request, or when the request was made\.

For more information on data sharing information in CloudTrail, see [Data sharing information in CloudTrail](https://docs.aws.amazon.com/redshift/latest/mgmt/db-auditing.html#rs-db-auditing-cloud-trail.html)\.

For more information about CloudTrail, see [How CloudTrail Works ](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html)\.