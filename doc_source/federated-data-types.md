# Data Type Differences Between Amazon Redshift and Supported RDS PostgreSQL or Aurora PostgreSQL Databases<a name="federated-data-types"></a>


|  | 
| --- |
| This is prerelease documentation for the federated query feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

The following table shows the mapping of an Amazon Redshift data type to a corresponding Amazon RDS PostgreSQL or Aurora PostgreSQL data type\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/federated-data-types.html)

The following RDS PostgreSQL and Aurora PostgreSQL data types are converted to VARCHAR\(64K\) in Amazon Redshift: 
+ JSON, JSONB
+ Arrays
+ BIT, BIT VARYING
+ BYTEA
+ Composite types
+ Date and time types INTERVAL and TIME
+ Enumerated types
+ Monetary types
+ Network address types
+ Numeric types SERIAL, BIGSERIAL, SMALLSERIAL, and MONEY 
+ Object identifier types
+ pg\_lsn type
+ Pseudo\-types
+ Range types
+ Text search types
+ TXID\_SNAPSHOT
+ UUID
+ XML type 