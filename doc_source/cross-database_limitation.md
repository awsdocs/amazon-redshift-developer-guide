# Limitations<a name="cross-database_limitation"></a>

When you work with the cross\-database query feature in Amazon Redshift, be aware of the limitations following:
+ When you query database objects on any other unconnected databases, you have read access only to those database objects\. 
+ Amazon Redshift doesn't support creating views that span objects across databases\. You can't query views that are created on a producer cluster which references to an object on a different producer cluster\. 
+ You can only create late\-binding and materialized views on consumer clusters that refer to tables on the producer clusters\. You can't create regular views that reference to tables on producer clusters\.
+ Amazon Redshift doesn't support tables with column\-level privileges for cross\-database queries\.
+ Amazon Redshift doesn't support concurrency scaling for the queries that read data from other databases\.
+ Amazon Redshift doesn't support query catalog objects on AWS Glue or federated databases\. To query these objects, first create external schemas that refer to those external data sources in each database\.
+ Amazon Redshift doesn't support result cache for cross\-database queries\.