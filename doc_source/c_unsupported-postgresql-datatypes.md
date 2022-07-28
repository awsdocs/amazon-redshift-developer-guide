# Unsupported PostgreSQL data types<a name="c_unsupported-postgresql-datatypes"></a>

Generally, if a query attempts to use an unsupported data type, including explicit or implicit casts, it will return an error\. However, some queries that use unsupported data types will run on the leader node but not on the compute nodes\. See [SQL functions supported on the leader node](c_sql-functions-leader-node.md)\.

 For a list of the supported data types, see [Data types](c_Supported_data_types.md)\.

These PostgreSQL data types are not supported in Amazon Redshift\.
+ Arrays
+ BIT, BIT VARYING
+ BYTEA
+ Composite Types
+ Date/Time Types
  +  INTERVAL
+ Enumerated Types
+ Geometric Types
+ HSTORE
+ JSON
+ Network Address Types
+ Numeric Types
  + SERIAL, BIGSERIAL, SMALLSERIAL
  + MONEY
+ Object Identifier Types
+ Pseudo\-Types
+ Range Types
+ Special Character Types
  + "char" – A single\-byte internal type \(where the data type named char is enclosed in quotation marks\)\.
  + name – An internal type for object names\.

  For more information about these types, see [Special Character Types](https://www.postgresql.org/docs/8.0/datatype-character.html) in the PostgreSQL documentation\. 
+ Text Search Types
+ TXID\_SNAPSHOT
+ UUID
+ XML