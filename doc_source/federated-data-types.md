# Data type differences between Amazon Redshift and supported PostgreSQL and MySQL databases<a name="federated-data-types"></a>

The following table shows the mapping of an Amazon Redshift data type to a corresponding Amazon RDS PostgreSQL or Aurora PostgreSQL data type\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/federated-data-types.html)

The following RDS PostgreSQL and Aurora PostgreSQL data types are converted to VARCHAR\(64K\) in Amazon Redshift: 
+ JSON, JSONB
+ Arrays
+ BIT, BIT VARYING
+ BYTEA
+ Composite types
+ Date and time types INTERVAL, TIME, TIME WITH TIMEZONE
+ Enumerated types
+ Monetary types
+ Network address types
+ Numeric types SERIAL, BIGSERIAL, SMALLSERIAL, and MONEY 
+ Object identifier types
+ pg\_lsn type
+ Pseudotypes
+ Range types
+ Text search types
+ TXID\_SNAPSHOT
+ UUID
+ XML type 

The following table shows the mapping of an Amazon Redshift data type to a corresponding Amazon RDS MySQL or Aurora MySQL data type\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/federated-data-types.html)

An error results when TIME data is out of range \(00:00:00 – 24:00:00\)\.

The following RDS MySQL and Aurora MySQL data types are converted to VARCHAR\(64K\) in Amazon Redshift: 
+ BIT
+ BINARY
+ VARBINARY
+ TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB
+ TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT
+ ENUM
+ SET
+ SPATIAL
