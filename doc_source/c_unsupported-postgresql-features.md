# Unsupported PostgreSQL features<a name="c_unsupported-postgresql-features"></a>

These PostgreSQL features are not supported in Amazon Redshift\.

**Important**  
Do not assume that the semantics of elements that Amazon Redshift and PostgreSQL have in common are identical\. Make sure to consult the *Amazon Redshift Developer Guide * [SQL commands](c_SQL_commands.md) to understand the often subtle differences\.
+ The query tool *psql* is unsupported\. The [Amazon Redshift RSQL](https://docs.aws.amazon.com/redshift/latest/mgmt/rsql-query-tool.html) client is supported\.
+ Table partitioning \(range and list partitioning\)
+ Tablespaces
+ Constraints
  + Unique
  + Foreign key
  + Primary key
  + Check constraints
  + Exclusion constraints

  Unique, primary key, and foreign key constraints are permitted, but they are informational only\. They are not enforced by the system, but they are used by the query planner\.
+ Database roles
+ Inheritance
+ Postgres system columns

  Amazon Redshift SQL does not implicitly define system columns\. However, the PostgreSQL system column names cannot be used as names of user\-defined columns\. See [https://www\.postgresql\.org/docs/8\.0/static/ddl\-system\-columns\.html](https://www.postgresql.org/docs/8.0/static/ddl-system-columns.html) 
+ Indexes
+ NULLS clause in Window functions
+ Collations

  Amazon Redshift does not support locale\-specific or user\-defined collation sequences\. See [Collation sequences](c_collation_sequences.md)\.
+ Value expressions
  + Subscripted expressions
  + Array constructors
  + Row constructors
+ Triggers
+ Management of External Data \(SQL/MED\)
+ Table functions
+ VALUES list used as constant tables
+ Sequences
+ Full text search