# Limitations and considerations when accessing federated data with Amazon Redshift<a name="federated-limitations"></a>

Some Amazon Redshift features don't support access to federated data\. You can find related limitations and considerations following\.

The following are limitations and considerations when using federated queries with Amazon Redshift: 
+ Federated queries support read access to external data sources\. You can't write or create database objects in the external data source\.
+ In some cases, you might access an Amazon RDS or Aurora database in a different AWS Region than Amazon Redshift\. In these cases, you typically incur network latency and billing charges for transferring data across AWS Regions\. We recommend using an Aurora global database with a local endpoint in the same AWS Region as your Amazon Redshift cluster\. Aurora global databases use dedicated infrastructure for storage\-based replication across any two AWS Regions with typical latency of less than 1 second\. 
+ Consider the cost of accessing Amazon RDS or Aurora\. For example, when using this feature to access Aurora, Aurora charges are based on IOPS\.
+ Federated queries don't enable access to Amazon Redshift from RDS or Aurora\. 
+ Federated queries currently don't support access through materialized views\. 
+ Federated queries are only available in AWS Regions where both Amazon Redshift and Amazon RDS or Aurora are available\. 
+ Federated queries currently don't support `ALTER SCHEMA`\. To change a schema, use `DROP` and then `CREATE EXTERNAL SCHEMA`\. 
+ Federated queries don't work with concurrency scaling\. 

The following are considerations for transactions when working with federated queries to PostgreSQL databases: 
+ If a query consists of federated tables, the leader node starts a READ ONLY REPEATABLE READ transaction on the remote database\. This transaction remains for the duration of the Amazon Redshift transaction\.
+ The leader node creates a snapshot of the remote database by calling `pg_export_snapshot` and makes a read lock on the affected tables\.
+ A compute node starts a transaction and uses the snapshot created at the leader node to issue queries to the remote database\.

An Amazon Redshift external schema can reference a database in an external RDS PostgreSQL or Aurora PostgreSQL\. When it does, these limitations apply: 
+ When creating an external schema referencing Aurora, the Aurora PostgreSQL database must be at version 9\.6, or later\. 
+ When creating an external schema referencing Amazon RDS, the Amazon RDS PostgreSQL database must be at version 9\.6, or later\. 


|  | 
| --- |
| The following is prerelease documentation for the federated query to MySQL feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Federated queries to RDS MySQL or Aurora MySQL support transaction isolation at the READ COMMITTED level\. 

An Amazon Redshift external schema can reference a database in an external RDS MySQL or Aurora MySQL\. When it does, these limitations apply: 
+ When creating an external schema referencing Aurora, the Aurora MySQL database must be at version 5\.6 or later\. 
+ When creating an external schema referencing Amazon RDS, the RDS MySQL database must be at version 5\.6 or later\. 