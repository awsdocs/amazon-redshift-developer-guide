# Limitations and Considerations When Accessing Federated Data with Amazon Redshift<a name="federated-limitations"></a>


|  | 
| --- |
| This is prerelease documentation for the federated query feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

The following are limitations and considerations when using federated query with Amazon Redshift: 
+ This feature supports read access to external data sources\. You can't write or create database objects in the external data source\.
+ In some cases, you might access an Amazon RDS or Aurora database in a different AWS Region than Amazon Redshift\. In these cases, you typically incur network latency and billing charges for transferring data across AWS Regions\. We recommend using an Aurora global database with a local endpoint in the same AWS Region as your Amazon Redshift cluster\. Aurora global databases use dedicated infrastructure for storage\-based replication across any two AWS Regions with typical latency of less than 1 second\. 
+ Consider the cost of accessing Amazon RDS or Aurora\. For example, when using this feature to access Aurora, Aurora charges are based on IOPS\.
+ This feature doesn't enable access to Amazon Redshift from RDS PostgreSQL or Aurora PostgreSQL\. 
+ This feature currently doesn't support access through materialized views\. 
+ This feature is only available in AWS Regions where both Amazon Redshift and Amazon RDS or Aurora are available\. 
+ This feature currently doesn't support `ALTER SCHEMA`\. You must `DROP` and then `CREATE EXTERNAL SCHEMA`\. 
+ Concurrency scaling is not supported\. 