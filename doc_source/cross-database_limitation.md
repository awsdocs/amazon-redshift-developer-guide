# Limitations<a name="cross-database_limitation"></a>

When you work with the cross\-database query feature in Amazon Redshift, be aware of the limitations following:
+ When you query database objects on any other unconnected databases, you have read access only to those database objects\. 
+ You can't query views that are created on other databases that refer to objects of yet another database\.
+ You can only create late\-binding and materialized views on objects of other databases in the cluster\. You can't create regular views on objects of other databases in the cluster\.
+ Amazon Redshift doesn't support tables with column\-level privileges for cross\-database queries\.
+ Amazon Redshift doesn't support concurrency scaling for the queries that read data from other databases\.
+ Amazon Redshift doesn't support query catalog objects on AWS Glue or federated databases\. To query these objects, first create external schemas that refer to those external data sources in each database\.
+ Amazon Redshift doesn't support result cache for cross\-database queries\.