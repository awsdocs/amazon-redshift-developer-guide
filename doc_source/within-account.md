# Sharing data within an account<a name="within-account"></a>

You can share data for read purposes across different Amazon Redshift clusters within an AWS account\.

**To share data for read purposes as a producer cluster administrator or database owner**

1. Create datashares in your cluster\. For more information, see [CREATE DATASHARE](r_CREATE_DATASHARE.md)\.

   ```
   CREATE DATASHARE SalesShare;
   ```

   Cluster superuser and database owners can create datashares\. Each datashare is associated with a database during creation\. Only objects from that database can be shared in that datashare\. Multiple datashares can be created on the same database with the same or different granularity of objects\. There is no limit on the number of datashares a cluster can create\.

   You can also use the Amazon Redshift console to create datashares\. For more information, see [Creating datashares](create-datashare-console.md)\.

1. Delegate permissions to operate on the datashare\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   The following example grants permissions to dbuser on SalesShare\.

   ```
   GRANT ALTER, SHARE ON DATASHARE SalesShare TO dbuser;
   ```

   Cluster superusers and the owners of the datashare can grant or revoke modification privileges on the datashare to additional users\.

1. Add objects to or remove objects from datashares\. To add objects to a datashare, add the schema before adding objects\. When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   ```
   ALTER DATASHARE SalesShare ADD SCHEMA PUBLIC;
   ALTER DATASHARE SalesShare ADD TABLE public.tickit_sales_redshift;
   ALTER DATASHARE SalesShare ADD ALL TABLES IN SCHEMA PUBLIC;
   ```

   You can also add views to a datashare\.

   ```
   CREATE VIEW public.sales_data_summary_view AS SELECT * FROM public.tickit_sales_redshift;
   ALTER DATASHARE SalesShare ADD TABLE public.sales_data_summary_view;
   ```

   Use ALTER DATASHARE to share schemas, and tables, views, and functions in a given schema\. Superusers, datashare owners, or users who have ALTER or ALL privilege on the datashare can alter the datashare to add objects to or remove objects from it\. Users should have the privileges to add or remove objects from the datashare\. Users should also be the owners of the objects or have SELECT, USAGE or ALL privileges on the objects\.

   Use the INCLUDENEW clause to add any new tables, views, or SQL user\-defined functions \(UDFs\) created in a specified schema to the datashare\. Only superusers can change this property for each datashare\-schema pair\.

   ```
   ALTER DATASHARE SalesShare ADD SCHEMA PUBLIC;
   ALTER DATASHARE SalesShare SET INCLUDENEW = TRUE FOR SCHEMA PUBLIC;
   ```

   You can also use the Amazon Redshift console to add or remove objects from datashares\. For more information, see [Adding datashare objects to datashares](add-datashare-object-console.md), [Removing datashare objects from datashares](remove-datashare-object-console.md), and [Editing datashares created in your account](edit-datashare-console.md)\.

1. Add consumers to or remove consumers from datashares\. The following example adds the consumer cluster namespace to the SalesShare\. The namespace is the namespace GUID of the consumer cluster in the account\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATASHARE SalesShare TO NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   ```

   You can only grant privileges to one datashare consumer in a GRANT statement\.

   Cluster superusers and the owners of datashare objects or users that have SHARE privilege on the datashare can add consumers to or remove consumers from a datashare\. To do so, they use GRANT USAGE or REVOKE USAGE\.

   To find the namespace of the cluster that you currently see, you can use the SELECT CURRENT\_NAMESPACE command\. To find the namespace of a different cluster within the same AWS account, go to the Amazon Redshift console cluster details page\. On that page, find the newly added namespace field\.

   You can also use the Amazon Redshift console to add or remove data consumers from datashares\. For more information, see [Adding data consumers to datashares](add-data-consumer-console.md) and [Removing data consumers from datashares](remove-data-consumer-console.md)\.

1. \(Optional\) Add security restrictions to the datashare\. The following example shows that the consumer cluster with a public IP access is allowed to read the datashare\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   ```
   ALTER DATASHARE Salesshare SET PUBLICACCESSIBLE = TRUE;
   ```

   You can modify properties about the type of consumers after datashare creation\. For example, you can define that clusters that want to consume data from a given datashare can't be publicly accessible\. Queries from consumer clusters that don't meet security restrictions specified in datashare are rejected at query execution time\.

   You can also use the Amazon Redshift console to edit datashares\. For more information, see [Editing datashares created in your account](edit-datashare-console.md)\.

1. List datashares created in the cluster and look into the contents of the datashare\.

   The following example displays the information of a datashare named SalesShare\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   ```
   DESC DATASHARE SalesShare;
                  
    producer_account  |          producer_namespace          | share_type | share_name | object_type |           object_name          |   include_new
   -------------------+--------------------------------------+------------+------------+-------------+--------------------------------+-------------------
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_users_redshift   |   
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_venue_redshift   |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_category_redshift|
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_date_redshift    |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_event_redshift   |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_listing_redshift |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | table       | public.tickit_sales_redshift   |
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | schema      | public                         |  t
    123456789012      | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | OUTBOUND   | salesshare | view        | public.sales_data_summary_view |
   ```

   The following example displays the outbound datashares in a producer cluster\.

   ```
   SHOW DATASHARES LIKE 'sales%';
   ```

   The output looks similar to the following\.

   ```
   share_name | share_owner  | source_database | consumer_database | share_type |     createdate      | is_publicaccessible  | share_acl | producer_account |          producer_namespace 
   -----------+--------------+-----------------+-------------------+------------+---------------------+----------------------+-----------+------------------+---------------------------------------
   salesshare |    100       | dev             |                   |  OUTBOUND  | 2020-12-09 02:27:08 |          True        |           |   123456789012   | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d
   ```

   For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\. 

   You can also use [SVV\_DATASHARES](r_SVV_DATASHARES.md), [SVV\_DATASHARE\_CONSUMERS](r_SVV_DATASHARE_CONSUMERS.md), and [SVV\_DATASHARE\_OBJECTS](r_SVV_DATASHARE_OBJECTS.md) to view the datashares, the objects within the datashare, and the datashare consumers\.

1. Drop datashares\. For more information, see [DROP DATASHARE](r_DROP_DATASHARE.md)\.

   You can delete the datashare objects at any point using [DROP DATASHARE](r_DROP_DATASHARE.md)\. Cluster superusers and owners of datashare can drop datashares\.

   The following example drops a datashare named SalesShare\.

   ```
   DROP DATASHARE SalesShare;
   ```

   You can also use the Amazon Redshift console to delete datashares\. For more information, see [Deleting datashares created in your account](delete-datashare-console.md)\.

1. Use ALTER DATASHARE to remove objects from datashares at any point from the datashare\. Use REVOKE USAGE ON to revoke permissions on the datashare to certain consumers\. It revokes USAGE privileges on objects within a datashare and instantly disables access to all consumer clusters\. Listing datashares and the metadata queries, such as listing databases and tables, doesn't return the shared objects after access is revoked\.

   ```
   ALTER DATASHARE SalesShare REMOVE TABLE public.tickit_sales_redshift;
   ```

   You can also use the Amazon Redshift console to edit datashares\. For more information, see [Editing datashares created in your account](edit-datashare-console.md)\.

1. Revoke access to the datashare from namespaces if you don't want to share the data with the consumers anymore\.

   ```
   REVOKE USAGE ON DATASHARE SalesShare FROM NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   ```

   You can also use the Amazon Redshift console to edit datashares\. For more information, see [Editing datashares created in your account](edit-datashare-console.md)\.

**To share data for read purposes as a consumer cluster administrator**

1. List the datashares that are made available to you and view the content of datashares\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   The following example displays the information of inbound datashares of a specified producer namespace\. When you run DESC DATASHARE as a consumer cluster administrator, you must specify the NAMESPACE option to view inbound datashares\. 

   ```
   DESC DATASHARE SalesShare OF NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   
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

1. Create local databases that reference to the datashares\. For more information, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.

   ```
   CREATE DATABASE Sales_db FROM DATASHARE SalesShare OF NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
   ```

   You can see databases that you created from the datashare by querying the [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md) view\. You can't connect to these databases created from datashares, and they are read\-only\. However, you can connect to a local database on your consumer cluster and perform a cross\-database query to query the data from the databases created from datashares\. You can't create a datashare on top of database objects created from an existing datashare\. However, you can copy the data into a separate table on the consumer cluster, perform any processing needed, and then share the new objects that were created\.

   You can also use the Amazon Redshift console to create databases from datashares\. For more information, see [Creating databases from datashares](create-database-from-datashare-console.md)\.

1. \(Optional\) Create external schemas to refer to and assign granular permissions to specific schemas in the consumer database imported on the consumer cluster\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

   ```
   CREATE EXTERNAL SCHEMA Sales_schema FROM REDSHIFT DATABASE 'Sales_db' SCHEMA 'public';
   ```

1. Grant permissions on databases and schema references created from the datashares to users groups in the consumer cluster as needed\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATABASE Sales_db TO Bob;
   ```

   ```
   GRANT USAGE ON SCHEMA Sales_schema TO GROUP Analyst_group;
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