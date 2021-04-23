# Considerations<a name="cross-database_usage"></a>

When you work with the cross\-database query feature in Amazon Redshift, consider the following:
+ Amazon Redshift supports cross\-database query on the ra3\.4xlarge, ra3\.16xlarge, and ra3\.xlplus node types\.
+ Amazon Redshift supports joining data from tables or views across one or more databases in the same Amazon Redshift cluster\.
+ All queries in a transaction on the connected database read data in the same state of the other database as the data was at the beginning of the transaction\. This approach helps to provide query transactional consistency across databases\. Amazon Redshift supports transactional consistency for cross\-database queries\. 
+ Amazon Redshift cross\-database queries using the three\-part notation don't support metadata tables under the schemas `information_schema` and `pg_catalog`, because these metadata views are specific to a database\. For applications and tools that integrate with cross\-database queries, get metadata across databases either by using SVV\_ALL\* or SVV\_REDSHIFT\* metadata views or by integrating with JDBC or ODBC drivers\.