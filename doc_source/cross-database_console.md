# Using cross\-database queries with the query editor<a name="cross-database_console"></a>

You can use cross\-database queries to access data from any of the databases on your Amazon Redshift cluster without having to connect to that specific database\. When you run cross\-database queries on any other unconnected databases, you have read access only to those database objects\.

You can query other database objects using fully qualified object names expressed with three\-part notation\. The full path to any database object consists of three components: database name, schema, and name of the object\. An example is *`database_name.schema.object_name`*\.

**To use cross\-database queries with the query editor**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. Create a cluster to use cross\-database queries in the Amazon Redshift query editor\. For more information, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster.html) in the *Amazon Redshift Cluster Management Guide*\.

1. Enable access to the query editor with the appropriate permissions\. For more information, see [Querying a database using the query editor](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor.html) in the *Amazon Redshift Cluster Management Guide*\.

1. On the navigation menu, choose **EDITOR**, then connect to a database in your cluster\.

1. View your database and any cross\-database queries\. 

   When you connect to the query editor for the first time, Amazon Redshift shows the resources for the connected database by default\. With the appropriate permissions, you can choose all the other databases that you have access to view the database objects\. After you choose a database, Amazon Redshift shows the list of schemas from the database\. 

   After you select a schema, you can see the list of database objects within that\. 
**Note**  
Amazon Redshift doesn't directly support query catalog objects that are part of AWS Glue or federated databases\. To query these, first create external schemas that refer to those external data sources in each database\.  
Amazon Redshift cross\-database queries with three\-part notation don't support metadata table under the schemas `information_schema` and `pg_catalog` because these metadata views are specific to a database\.

1. \(Optional\) Choose another database to view different data\.

1. \(Optional\) Filter the list of tables or views from the specified schema\.