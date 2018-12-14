# Unsupported PostgreSQL Data Types<a name="c_unsupported-postgresql-datatypes"></a>

Generally, if a query attempts to use an unsupported data type, including explicit or implicit casts, it will return an error\. However, some queries that use unsupported data types will run on the leader node but not on the compute nodes\. See [SQL Functions Supported on the Leader Node](c_sql-functions-leader-node.md)\.

 For a list of the supported data types, see [Data Types](c_Supported_data_types.md)\.

These PostgreSQL data types are not supported in Amazon Redshift\.
+ Arrays
+ BIT, BIT VARYING
+ BYTEA
+ Composite Types
+ Date/Time Types
  +  INTERVAL
  + TIME
+ Enumerated Types
+ Geometric Types
+ JSON
+ Network Address Types
+ Numeric Types
  + SERIAL, BIGSERIAL, SMALLSERIAL
  + MONEY
+ Object Identifier Types
+ Pseudo\-Types
+ Range Types
+ Text Search Types
+ TXID\_SNAPSHOT
+ UUID
+ XML