# Consumer cluster administrator actions<a name="consumer-cluster-admin"></a>

**If you are a consumer cluster administrator** – follow these steps:

1. List the datashares made available to you and view the content of datashares\. The content of datashares is available only when the producer cluster administrator has authorized the datashares and the consumer cluster administrator has accepted and associated the datashares\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   The following example displays the information of inbound datashares of a specified producer namespace\. When you run the DESC DATAHSARE as a consumer cluster administrator, you must specify the NAMESPACE and account ID to view inbound datashares\. For outbound datashares, specify the datashare name\.

   ```
   SHOW DATASHARES LIKE 'sales%';
   
   share_name | share_owner | source_database | consumer_database | share_type | createdate | is_publicaccessible | share_acl | producer_account |          producer_namespace
   -----------+-------------+-----------------+-------------------+------------+------------+---------------------+-----------+------------------+---------------------------------------
   salesshare |             |                 |                   | INBOUND    |            |        t            |           | 123456789012    | 'dd8772e1-d792-4fa4-996b-1870577efc0d'
   ```

   ```
   DESC DATASHARE salesshare OF ACCOUNT '123456789012' NAMESPACE 'dd8772e1-d792-4fa4-996b-1870577efc0d';
   
   
    producer_account |          producer_namespace          | share_type | share_name | object_type |           object_name
   ------------------+--------------------------------------+------------+------------+-------------+---------------------------------
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_users_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_venue_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_category_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_date_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_event_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_listing_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_sales_redshift
    123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | schema      | public
   (8 rows)
   ```

   Only cluster superusers can do this\. You can also use SVV\_DATASHARES to view the datashares and SVV\_DATASHARE\_OBJECTS to view the objects within the datashare\.

   The following example displays the inbound datashares in a consumer cluster\.

   ```
   SELECT * FROM SVV_DATASHARES WHERE share_name LIKE 'sales%';
   
   share_name | share_owner | source_database | consumer_database | share_type | createdate | is_publicaccessible | share_acl | producer_account |          producer_namespace
   -----------+-------------+-----------------+-------------------+------------+------------+---------------------+-----------+------------------+---------------------------------------
   salesshare |             |                 |                   | INBOUND    |            |        t            |           | 123456789012      | 'dd8772e1-d792-4fa4-996b-1870577efc0d'
   ```

   ```
   SELECT * FROM SVV_DATASHARE_OBJECTS WHERE share_name LIKE 'sales%';
    share_type | share_name | object_type |           object_name           | producer_account |          producer_namespace
   ------------+------------+-------------+---------------------------------+------------------+--------------------------------------
    INBOUND    | salesshare | table       | public.tickit_users_redshift    | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_venue_redshift    | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_category_redshift | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_date_redshift     | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_event_redshift    | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_listing_redshift  | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | table       | public.tickit_sales_redshift    | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
    INBOUND    | salesshare | schema      | public                          | 123456789012     | dd8772e1-d792-4fa4-996b-1870577efc0d
   (8 rows)
   ```

1. Create local databases that reference to the datashares\. Specify the NAMESPACE and account ID when creating the database from the datashare\. For more information, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.

   ```
   CREATE DATABASE sales_db FROM DATASHARE salesshare OF ACCOUNT '123456789012' NAMESPACE 'dd8772e1-d792-4fa4-996b-1870577efc0d';
   ```

   You can see databases that you created from the datashare by querying [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md) view\. You can't connect to these databases created from datashares, and they are read\-only\. However, you can connect to a local database on your consumer cluster and perform a cross\-database query to query the data from the databases created from datashares\. You can't create a datashare on top of database objects created from an existing datashare\. However, you can copy the data into a separate table on the consumer cluster, perform any processing needed, and then share the new objects created\.

1. \(Optional\) Create external schemas to refer and assign granular permissions to specific schemas in the consumer database imported on the consumer cluster\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

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

   As a consumer cluster administrator, you can only assign permissions on the entire database created from the datashare to your users and groups\. In some cases, you need fine\-grained controls on a subset of database objects created from the datashare\. If so, you can create an external schema reference pointing to specific schemas in the datashare, as described in the previous step\. You can then provide granular permissions at the schema level\. You can also create late\-binding views on top of shared objects and use these to assign granular permissions\. You can also consider having producer clusters create additional datashares for you with the granularity required\. You can create as many schema references to the database created from the datashare\.

1. Query data in the shared objects in the datashares\.

   Users and groups with permissions on consumer databases and schemas on consumer clusters can explore and navigate the metadata of any shared objects\. They can also explore and navigate local objects in a consumer cluster\. To do this, use JDBC or ODBC drivers or SVV\_ALL and SVV\_REDSHIFT views\.

   Producer clusters might have many schemas in the database, tables, and views within each schema\. The users on the consumer side can see only the subset of objects that are made available through the datashare\. These users can't see all the metadata from the producer cluster\. This approach helps provide granular metadata security control with data sharing\.

   You continue to connect to local cluster databases\. But now, you can also read from the databases and schemas that are created from the datashare using the three\-part database\.schema\.table notation\. You can perform queries that span across any and all databases that are visible to you\. These can be local databases on the cluster or databases created from the datashares\. Consumer clusters can't connect to the databases created from the datashares\.

   You can access the data using full qualification\. For more information, see [Examples of using a cross\-database query](cross-database_example.md)\.

   ```
   SELECT * FROM sales_db.public.tickit_sales_redshift;
   ```

   You can only use SELECT statements on shared objects\. However, you can create tables in the consumer cluster by querying the data from the shared objects in a different local database\.

   In addition to performing queries, consumers can create views on shared objects\. Only late\-binding views and materialized views are supported\. Amazon Redshift doesn't support regular views on shared data\. Views that consumers create can span across multiple local databases or databases created from datashares\. For more information, see [CREATE VIEW](r_CREATE_VIEW.md)\.

   ```
   // Connect to a local cluster database
                  
   // Create a view on shared objects and access it. 
   CREATE VIEW sales_data 
   AS SELECT * 
   FROM sales_db.public.tickit_sales_redshift 
   WITH NO SCHEMA BINDING;
   
   SELECT * FROM sales_data;
   ```