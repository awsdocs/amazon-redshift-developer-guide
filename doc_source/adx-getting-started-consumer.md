# Working with AWS Data Exchange datashares as a consumer<a name="adx-getting-started-consumer"></a>

**If you are a consumer, follow these steps to discover data products that contain AWS Data Exchange datashares and query Amazon Redshift data:**

1. On the AWS Data Exchange console, discover and subscribe to data products that contains AWS Data Exchange datashares\.

   Once your subscription starts, you can access licensed Amazon Redshift data that is imported as assets to datasets that contain AWS Data Exchange datashares\.

   For more information on how to get started with using data products that contain AWS Data Exchange datashares, see [Subscribing to data products on AWS Data Exchange](https://https://docs.aws.amazon.com/data-exchange/latest/userguide/subscribe-to-data-sets.html)\.

1. On the Amazon Redshift console, create an Amazon Redshift cluster, if needed\.

   For information on how to create a cluster, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster)\.

1. List the datashares that are made available to you and view the content of datashares\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   The following example displays the information of inbound datashares of a specified producer namespace\. When you run DESC DATASHARE as a consumer cluster administrator, you must specify the ACCOUNT and NAMESPACE option to view inbound datashares\. 

   ```
   DESC DATASHARE salesshare of ACCOUNT '123456789012' NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   
    producer_account  |          producer_namespace          | share_type | share_name | object_type |           object_name           |   include_new
   -------------------+--------------------------------------+------------+------------+-------------+---------------------------------+------------------
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_users_redshift    |     
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_venue_redshift    |     
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_category_redshift |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_date_redshift     |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_event_redshift    |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_listing_redshift  |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | table       | public.tickit_sales_redshift    |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | schema      | public                          |    
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | INBOUND    | salesshare | view        | public.sales_data_summary_view  |
   ```

   Only cluster superusers can do this\. You can also use SVV\_DATASHARES to view the datashares and SVV\_DATASHARE\_OBJECTS to view the objects within the datashare\.

   The following example displays the inbound datashares in a consumer cluster\.

   ```
   SHOW DATASHARES LIKE 'sales%';
   
   
    share_name | share_owner | source_database | consumer_database | share_type | createdate | is_publicaccessible | share_acl | producer_account |          producer_namespace
   ------------+-------------+-----------------+-------------------+------------+------------+---------------------+-----------+------------------+--------------------------------------
    salesshare |             |                 |                   | INBOUND    |            |         t           |           |   123456789012   | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d
   ```

1. Create local databases that reference to the datashares\. You must specify the ACCOUNT and NAMESPACE option to create local databases for AWS Data Exchange datashares\. For more information, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.

   ```
   CREATE DATABASE sales_db FROM DATASHARE salesshare OF ACCOUNT '123456789012' NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   ```

   You can see databases that you created from the datashare by querying the [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md) view\. You can't connect to these databases created from datashares, and they are read\-only\. However, you can connect to a local database on your consumer cluster and perform a cross\-database query on the data from the databases created from datashares\. You can't create a datashare on top of database objects created from an existing datashare\. However, you can copy the data into a separate table on the consumer cluster, perform any processing needed, and then share the new objects that were created\.

   You can also use the Amazon Redshift console to create databases from datashares\. For more information, see [Creating databases from datashares](create-database-from-datashare-console.md)\.

1. \(Optional\) Create external schemas to refer to and assign granular permissions to specific schemas in the consumer database imported on the consumer cluster\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

   ```
   CREATE EXTERNAL SCHEMA sales_schema FROM REDSHIFT DATABASE 'sales_db' SCHEMA 'public';
   ```

1. Grant permissions on databases and schema references created from the datashares to users groups in the consumer cluster as needed\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATABASE sales_db TO Bob;
   ```

   ```
   GRANT USAGE ON SCHEMA sales_schema TO GROUP Analyst_group;
   ```

   As a consumer cluster administrator, you can only assign permissions on the entire database created from the datashare to your users and groups\. In some cases, you need fine\-grained controls on a subset of database objects created from the datashare\. If so, you can create an external schema reference that points to specific schemas in the datashare \(as described in the previous step\) and provide granular permissions at schema level\. 

   You can also create late\-binding views on top of shared objects and use these to assign granular permissions\. You can also consider having producer clusters create additional datashares for you with the granularity required\. You can create as many schema references to the database created from the datashare\.

1. Query data in the shared objects in the datashares\.

   Users and groups with permissions on consumer databases and schemas on consumer clusters can explore and navigate the metadata of any shared objects\. They can also explore and navigate local objects in a consumer cluster\. To do this, they use JDBC or ODBC drivers or SVV\_ALL and SVV\_REDSHIFT views\.

   Producer clusters might have many schemas in the database, tables, and views within each schema\. The users on the consumer side can see only the subset of objects that are made available through the datashare\. These users can't see the entire metadata from the producer cluster\. This approach helps provide granular metadata security control with data sharing\.

   You continue to connect to local cluster databases\. But now, you can also read from the databases and schemas that are created from the datashare using the three\-part database\.schema\.table notation\. You can perform queries that span across any and all databases that are visible to you\. These can be local databases on the cluster or databases created from the datashares\. Consumer clusters can't connect to the databases created from the datashares\.

   You can access the data using full qualification\. For more information, see [Examples of using a cross\-database query](cross-database_example.md)\.

   ```
   SELECT * FROM sales_db.public.tickit_sales_redshift ORDER BY 1,2 LIMIT 5;
   
    salesid | listid | sellerid | buyerid | eventid | dateid | qtysold | pricepaid | commission |      saletime
   ---------+--------+----------+---------+---------+--------+---------+-----------+------------+---------------------
          1 |      1 |    36861 |   21191 |    7872 |   1875 |       4 |    728.00 |     109.20 | 2008-02-18 02:36:48
          2 |      4 |     8117 |   11498 |    4337 |   1983 |       2 |     76.00 |      11.40 | 2008-06-06 05:00:16
          3 |      5 |     1616 |   17433 |    8647 |   1983 |       2 |    350.00 |      52.50 | 2008-06-06 08:26:17
          4 |      5 |     1616 |   19715 |    8647 |   1986 |       1 |    175.00 |      26.25 | 2008-06-09 08:38:52
          5 |      6 |    47402 |   14115 |    8240 |   2069 |       2 |    154.00 |      23.10 | 2008-08-31 09:17:02
   ```

   You can only use SELECT statements on shared objects\. However, you can create tables in the consumer cluster by querying the data from the shared objects in a different local database\.

   In addition to queries, consumers can create views on shared objects\. Only late\-binding views or materialized views are supported\. Amazon Redshift doesn't support regular views on shared data\. Views that consumers create can span across multiple local databases or databases created from datashares\. For more information, see [CREATE VIEW](r_CREATE_VIEW.md)\.

   ```
   // Connect to a local cluster database
                  
   // Create a view on shared objects and access it. 
   CREATE VIEW sales_data 
   AS SELECT * 
   FROM sales_db.public.tickit_sales_redshift 
   WITH NO SCHEMA BINDING;
   
   SELECT * FROM sales_data;
   ```