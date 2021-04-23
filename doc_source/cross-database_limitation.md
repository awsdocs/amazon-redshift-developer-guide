# Limitations<a name="cross-database_limitation"></a>

When you work with the cross\-database query feature in Amazon Redshift, be aware of the limitations following:
+ Amazon Redshift doesn't support creating views that span objects across databases\.
+ Amazon Redshift doesn't support tables with column level privileges for cross\-database queries\.
+ Amazon Redshift doesn't support concurrency scaling for the queries that read data from other databases\.
+ Amazon Redshift doesn't support query catalog objects on AWS Glue or federated databases\. To query these objects, first create external schemas that refer to those external data sources in each database\.
+ Amazon Redshift doesn't support result cache for cross\-database queries\.