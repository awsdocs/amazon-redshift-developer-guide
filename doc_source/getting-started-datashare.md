# Getting started data sharing<a name="getting-started-datashare"></a>

**Topics**
+ [Sharing data within an account](#within-account)

To start sharing data within an account, take the steps following\.

## Sharing data within an account<a name="within-account"></a>

You can share data for read purposes across different Amazon Redshift clusters within an AWS account\.

**If you are a producer cluster administrator or database owner** – follow these steps:

1. Create data shares in your cluster\. For more information, see [CREATE DATASHARE](r_CREATE_DATASHARE.md)\.

   ```
   CREATE DATASHARE SalesShare;
   ```

   You can create an empty data share object to start the sharing process with consumer cluster\. Producer cluster superuser and database owners can create data share objects\. Each data share is associated with a database during creation\. Only objects from that database can be shared in that data share\. Multiple data shares can be created on the same database with the same or different granularity of objects\. There is no limit on the number of data shares a cluster can create\.

1. Delegate permissions to operate on the data share\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   Cluster superusers and the owners of the data share can grant or revoke modification privileges on the data share to additional users\.

1. Add objects to or remove objects from data shares\. To add objects to a data share, add the schema before adding objects\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   ```
   ALTER DATASHARE SalesShare ADD SCHEMA PUBLIC; 
   ALTER DATASHARE SalesShare ADD TABLE public.tickit_venue_redshift; 
   ALTER DATASHARE SalesShare ADD ALL TABLES IN SCHEMA PUBLIC;
   ```

   You can also add views to a data share\.

   ```
   ALTER DATASHARE SalesShare ADD TABLE public.sales_data_summary_view;
   ```

   Use ALTER DATASHARE to share the entire schema, to share tables, views, and functions in a given schema, and to share objects from multiple schemas\. Certain users can add objects to or remove objects from a data share\. These users include superusers and users who are owners\. These users also include those that have SELECT, USAGE, and ALL privileges on the objects and that also have ALTER privilege on the data share or the owner of data share\. 

1. Add consumers to or remove consumers from data shares\. The following example adds the consumer cluster namespace to the SalesShare\. The namespace is the namespace GUID of the consumer cluster in the account\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATASHARE Salesshare TO NAMESPACE 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b';
   ```

   Cluster superusers and the owners of database objects or users that have SHARE privilege on the data share can add consumers to or remove consumers from a data share\. To do so, they use GRANT USAGE or REVOKE USAGE\.

   To find the namespace of the cluster that you currently see, you can use the SELECT CURRENT\_NAMESPACE command\. To find the namespace of a different cluster within the same AWS account, go to the Amazon Redshift console cluster details page and refer to the newly added namespace field\.

1. \(Optional\) Add security restrictions to the data share\. The following example shows that the consumer cluster with a public IP access is allowed to read the data share\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   ```
   ALTER DATASHARE Salesshare SET PUBLICACCESSIBLE = TRUE;
   ```

   You can modify properties about the type of consumers after data share creation\. For example, you can define that clusters that want to consume data from a given data share can't be publicly accessible\. Queries from consumer clusters that don't meet security restrictions specified in data share are rejected at query execution time\.

1. List data shares created in the cluster and look into the contents of the data share\.

   The following example displays the information of a data share named SalesShare\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   ```
   DESC DATASHARE SalesShare;
             
   producer_account | producer_namespace                      | share_type  | share_name   | object_type | object_name 
   -----------------+-----------------------------------------+-------------+--------------+-------------+---------------------------------------------------
   '467896856988'   | 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b'  | OUTBOUND    | 'SalesShare' | TABLE       | 'public.ticket_venue_redshift'
   '467896856988'   | 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b'  | OUTBOUND    | 'SalesShare' | SCHEMA      | 'public'
   ```

   The following example displays the outbound data shares in a producer cluster\.

   ```
   SHOW DATASHARES LIKE 'sales%';
   ```

   The output looks similar to the following\.

   ```
   share_name   | share_owner  | source_database | consumer_database | share_type | createdate           | is_publicaccessible  | share_acl | producer_account | producer_namespace 
   -------------+--------------+-----------------+-------------------+------------+----------------------+----------------------+-----------+------------------+---------------------------------------
   'salesshare' | 100          | dev             |                   | outbound   | 2020-12-09 01:22:54. | False                |           | '467896856988'   | '5785235e-96d2-4da7-9f12-3b73652529df'
   ```

   For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\. 

   You can also use SVV\_DATASHARES, SVV\_DATASHARE\_OBJECTS, and SVV\_DATASHARE\_CONSUMERS to view the data shares, the objects within the data share and the data share consumers\.

1. Drop data shares\. For more information, see [DROP DATASHARE](r_DROP_DATASHARE.md)\.

   You can delete the data share objects at any point using DROP\. Cluster superusers and owners of data share can drop data shares\.

   The following example drops a data share named SalesShare\.

   ```
   DROP DATASHARE SalesShare;
   ```

1. Remove objects from data shares at any point from the data share or revoke permissions on the data share to certain consumers\. Use the ALTER DATASHARE, REMOVE USAGE ON syntax\. Revoking removes objects from the data share and instantly disables access to all consumer clusters\. Listing data shares and the metadata queries such as listing databases and tables don't return the shared objects once access is revoked\.

   ```
   ALTER DATASHARE SalesShare REMOVE TABLE public.tickit_venue_redshift;
   ```

1. Revoke access to the data share from namespaces if you don't want to share the data with the consumers anymore\.

   ```
   REVOKE USAGE ON DATASHARE SalesShare FROM NAMESPACE 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b';
   ```

**If you are a consumer cluster administrator** – follow these steps:

1. List the data shares made available to you and view the content of data shares\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   The following example displays the information of inbound data shares of a specified producer namespace\. When you run the DESC DATAHSARE as a consumer cluster administrator, you must specify the NAMESPACE option to view inbound data shares\. 

   ```
   DESC DATASHARE SalesShare OF NAMESPACE 'dd8772e1-d792-4fa4-996b-1870577efc0d';
   
    producer_account |          producer_namespace          | share_type | share_name | object_type |         object_name
   ------------------+--------------------------------------+------------+------------+-------------+------------------------------
    467896856988     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_venue_redshift
    467896856988     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | schema      | public
   ```

   Only cluster superusers can do this\. You can also use SVV\_DATASHARES to view the data shares and SVV\_DATASHARE\_OBJECTS to view the objects within the data share\.

   The following example displays the inbound data shares in a consumer cluster\.

   ```
   SHOW DATASHARES LIKE 'sales%';
   
   share_name | share_owner | source_database | consumer_database | share_type | createdate | is_publicaccessible | share_acl | producer_account |          producer_namespace
   -----------+-------------+-----------------+-------------------+------------+------------+---------------------+-----------+------------------+--------------------------------------
   salesshare |             |                 |                   | INBOUND    |            | t                   |           | 467896856988     | dd8772e1-d792-4fa4-996b-1870577efc0d
   ```

1. Create local databases that reference to the data shares\. For more information, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.

   ```
   CREATE DATABASE Sales_db FROM DATASHARE SalesShare OF NAMESPACE 'dd8772e1-d792-4fa4-996b-1870577efc0d';
   ```

   You can see databases that you created from the data share by querying SVV\_REDSHIFT\_DATABASES view\. You can't connect to these databases created from data shares and they are read\-only\. However, you can connect to a local database on your consumer cluster and perform cross\-database query to query the data from the databases created from data shares\. You can't create a data share on top of database objects created from an existing data share\. However, you can copy the data into a separate table on the consumer cluster and perform any processing needed and then share the new objects created\.

1. \(Optional\) Create external schemas to refer and assign granular permissions to specific schemas in the shared database imported on the consumer cluster\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

   ```
   CREATE EXTERNAL SCHEMA Sales_schema FROM REDSHIFT DATABASE 'Sales_db' SCHEMA 'public';
   ```

1. Grant permissions on databases and schema references created from the data shares to users groups in the consumer cluster as needed\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATABASE Sales_db TO Bob;
   ```

   ```
   GRANT USAGE ON SCHEMA Sales_schema TO GROUP Analyst_group;
   ```

   As a consumer cluster administrator, you can only assign permissions on the entire database created from the data share to your users and groups\. In some cases, you need fine\-grained controls on a subset of database objects created from the data share\. If so, you can create an external schema reference pointing to specific schemas in the data share as described in the previous step and provide granular permissions at schema level\. You can also create late binding views on top of shared objects and use these to assign granular permissions\. You can also consider having producer clusters create additional data shares for you with the granularity required\. You can create as many schema references to the database created from the data share\.

1. Query data in the shared objects in the data shares\.

   Through JDBC or ODBC drivers or SVV\_ALL and SVV\_REDSHIFT views, users and groups with permissions on shared databases and schemas on consumer cluster can explore and navigate the metadata of shared objects along with the local objects in the consumer cluster\. Producer cluster might have many schemas in the database, tables, and views within each schema\. The users on the consumer side can only see the subset of objects that are made available through the data share and not the entire metadata from the producer cluster providing granular metadata security control with data sharing\.

   You continue to connect to local cluster databases, but now you can also read from the databases and schemas that are created from the data share using the three\-part database\.schema\.table notation\. You can perform queries that span across any and all databases that are visible to you\. These can be local databases on the cluster or databases created from the data shares\. Consumer clusters can't connect to the databases created from the data shares\.

   You can access the data using full qualification\. For more information, see [Examples of using a cross\-database query](cross-database_example.md)\.

   ```
   SELECT * FROM sales_db.public.tickit_venue_redshift;
   ```

   You can only use SELECT statements on shared objects\. However, you can create tables in the consumer cluster by querying the data from the shared objects in a different local database\.

   In addition to queries, consumers can create views on shared objects\. Only late binding views or materialized views are supported\. Amazon Redshift doesn't support regular views on shared data\. Views that consumers create can span across multiple local databases or databases created from data shares\. For more information, see [CREATE VIEW](r_CREATE_VIEW.md)\.

   ```
   // Connect to a local cluster database
   CONNECT to myDATABASE
   // Create a view on shared objects and access it. 
   CREATE VIEW customer_data AS 
   SELECT * FROM sales_db.sch1.customer_data_tbl LATE BINDING;
   SELECT * FROM customer_data;
   ```