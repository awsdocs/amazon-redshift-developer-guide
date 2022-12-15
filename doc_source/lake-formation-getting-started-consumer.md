# Working with Lake Formation\-managed datashares as a consumer<a name="lake-formation-getting-started-consumer"></a>

 After the AWS Lake Formation administrator discovers the datashare invitation and creates a database in the AWS Glue Data Catalog that links to the datashare, the consumer cluster administrator can associate the cluster with the datashare and the database in the AWS Glue Data Catalog, create a database local to the consumer cluster, and assign permissions to users and groups in the Amazon Redshift consumer cluster to start querying\. Follow these steps to set up querying permissions\. 

1. On the Amazon Redshift console, create an Redshift cluster to serve as the consumer cluster, if needed\. For information on how to create a cluster, see [ Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster)\.

1. To list which databases in the AWS Glue Data Catalog consumer cluster users have access to, run the [SHOW DATABASES](https://docs.aws.amazon.com/redshift/latest/dg/r_SHOW_DATABASES.html) command\.

   ```
   SHOW DATABASES FROM DATA CATALOG [ACCOUNT <account-id>,<account-id2>] [LIKE <expression>]
   ```

   Doing so lists the resources that are available from the Data Catalog, such as the AWS Glue database’s ARN, database name, and information about the datashare\.

1. Using the AWS Glue database ARN from SHOW DATABASES, create a local database in the consumer cluster\. For more information, see [CREATE DATABASE](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_DATABASE.html)\.

   ```
   CREATE DATABASE lf_db FROM ARN <lake-formation-database-ARN> WITH [NO] DATA CATALOG SCHEMA [<schema>];
   ```

1. Grant permissions on databases and schema references created from the datashares to users and groups in the consumer cluster as needed\. For more information, see [GRANT](https://docs.aws.amazon.com/redshift/latest/dg/r_GRANT.html) or [REVOKE](https://docs.aws.amazon.com/redshift/latest/dg/r_REVOKE.html)\. Note that users created from the [CREATE USER](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_USER.html) command cannot access objects in datashare that have been shared to Lake Formation\. Only IAM users with access to both Redshift and Lake Formation can access datashares that have been shared with Lake Formation\. 

   ```
   GRANT USAGE ON DATABASE sales_db TO IAM:Bob;
   ```

    As a consumer cluster administrator, you can only assign permissions on the entire database created from the datashare to your users and groups\. In some cases, you need fine\-grained controls on a subset of database objects created from the datashare\. 

    You can also create late\-binding views on top of shared objects and use these to assign granular permissions\. You can also consider having producer clusters create additional datashares for you with the granularity required\. You can create as many schema references to the database created from the datashare\. 

1. Database users can use the views SVV\_EXTERNAL\_TABLES and SVV\_EXTERNAL\_COLUMNS to find all of the shared tables or columns within the AWS Glue database

   ```
   SELECT * from svv_external_tables WHERE redshift_database_name = 'lf_db';
                           
   SELECT * from svv_external_columns WHERE redshift_database_name = 'lf_db';
   ```

1. Query data in the shared objects in the datashares\.

   Users and groups with permissions on consumer databases and schemas on consumer clusters can explore and navigate the metadata of any shared objects\. They can also explore and navigate local objects in a consumer cluster\. To do so, they can use the JDBC or ODBC drivers or the SVV\_ALL and SVV\_EXTERNAL views\.

   ```
   SELECT * FROM lf_db.schema.table;
   ```

   You can only use SELECT statements on shared objects\. However, you can create tables in the consumer cluster by querying the data from the shared objects in a different local database\.

   ```
   // Connect to a local cluster database
               
   // Create a view on shared objects and access it. 
   CREATE VIEW sales_data 
   AS SELECT * 
   FROM sales_db.public.tickit_sales_redshift 
   WITH NO SCHEMA BINDING;
   
   SELECT * FROM sales_data;
   ```