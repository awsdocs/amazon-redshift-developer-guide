# SYS\_SERVERLESS\_USAGE<a name="SYS_SERVERLESS_USAGE"></a>

Use SYS\_SERVERLESS\_USAGE to view details of Amazon Redshift Serverless usage of resources\. This system view doesn't apply to provisioned Amazon Redshift clusters\.

This view contains the serverless usage summary including how much compute capacity is used to process queries and the amount of Amazon Redshift managed storage used at a 30\-minute granularity\. The compute capacity is measured in Redshift processing units \(RPUs\) and metered for the workloads you run in RPU\-seconds on a per\-second basis\. RPUs are used to process queries on the data loaded in the data warehouse, queried from an Amazon S3 data lake, or accessed from operational databases using a federated query\.

SYS\_SERVERLESS\_USAGE is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="SYS_SERVERLESS_USAGE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_SERVERLESS_USAGE.html)