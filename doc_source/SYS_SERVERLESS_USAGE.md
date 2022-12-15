# SYS\_SERVERLESS\_USAGE<a name="SYS_SERVERLESS_USAGE"></a>

Use SYS\_SERVERLESS\_USAGE to view details of Amazon Redshift Serverless usage of resources\. This system view doesn't apply to provisioned Amazon Redshift clusters\.

This view contains the serverless usage summary including how much compute capacity is used to process queries and the amount of Amazon Redshift managed storage used at a 1\-minute granularity\. The compute capacity is measured in Redshift processing units \(RPUs\) and metered for the workloads that you run in RPU\-seconds on a per\-second basis\. RPUs are used to process queries on the data loaded in the data warehouse, queried from an Amazon S3 data lake, or accessed from operational databases using a federated query\. Amazon Redshift Serverless retains the information in SYS\_SERVERLESS\_USAGE for 7 days\.

For examples on compute cost billing, see [Illustrating compute cost billing scenario](https://docs.aws.amazon.com/redshift/latest/mgmt/serverless-billing-scenarios.html)\.

SYS\_SERVERLESS\_USAGE is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_SERVERLESS_USAGE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_SERVERLESS_USAGE.html)