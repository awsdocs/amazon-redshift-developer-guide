# Working with AWS Data Exchange datashares as a producer<a name="adx-getting-started-producer"></a>

**If you are a producer cluster administrator, follow these steps to manage AWS Data Exchange datashares on the Amazon Redshift console:**

1. Create datashares in your cluster to share data on AWS Data Exchange and grant access to AWS Data Exchange to the datashares\.

   Cluster superuser and database owners can create datashares\. Each datashare is associated with a database during creation\. Only objects from that database can be shared in that datashare\. Multiple datashares can be created on the same database with the same or different granularity of objects\. There is no limit on the number of datashares you can create on a cluster\.

   You can also use the Amazon Redshift console to create datashares\. For more information, see [Creating datashares](create-datashare-console.md)\.

   Use the MANAGEDBY ADX option to implicitly grant access of the datashare to AWS Data Exchange when running the CREATE DATASHARE statement\. This indicates that AWS Data Exchange manages this datashare\. You can only use the MANAGEDBY ADX option when you create a new datashare\. You can't use the ALTER DATASHARE statement to modify an existing datashare to add the MANAGEDBY ADX option\. Once a datashare is created with the MANAGEDBY ADX option, only AWS Data Exchange can access and manage the datashare\.

   ```
   CREATE DATASHARE salesshare
   [[SET] MANAGEDBY [=] {ADX} ];
   ```

1. Add objects to the datashares\. Producer administrator continues to manage datashare objects that are available in an AWS Data Exchange datashare\.

   To add objects to a datashare, add the schema before adding objects\. When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\. For more information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   ```
   ALTER DATASHARE salesshare ADD SCHEMA PUBLIC;
   ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;
   ALTER DATASHARE salesshare ADD ALL TABLES IN SCHEMA PUBLIC;
   ```

   You can also add views to a datashare\.

   ```
   CREATE VIEW public.sales_data_summary_view AS SELECT * FROM public.tickit_sales_redshift;
   ALTER DATASHARE salesshare ADD TABLE public.sales_data_summary_view;
   ```

   Use ALTER DATASHARE to share schemas, and tables, views, and functions in a given schema\. Superusers, datashare owners, or users who have ALTER or ALL privilege on the datashare can alter the datashare to add objects to or remove objects from it\. Users should have the permissions to add or remove objects from the datashare\. Users should also be the owners of the objects or have SELECT, USAGE or ALL permissions on the objects\.

   Use the INCLUDENEW clause to add any new tables, views, or SQL user\-defined functions \(UDFs\) created in a specified schema to the datashare\. Only superusers can change this property for each datashare\-schema pair\.

   ```
   ALTER DATASHARE salesshare ADD SCHEMA PUBLIC;
   ALTER DATASHARE salesshare SET INCLUDENEW = TRUE FOR SCHEMA PUBLIC;
   ```

   You can also use the Amazon Redshift console to add or remove objects from datashares\. For more information, see [Adding datashare objects to datashares](add-datashare-object-console.md), [Removing datashare objects from datashares](remove-datashare-object-console.md), and [Editing AWS Data Exchange datashares \(preview\) ](edit-adx-datashare-console.md)\.

1. To authorize access to the datashares for AWS Data Exchange, do one of the following:
   + Explicitly authorize access to the datashare for AWS Data Exchange by using the `ADX` keyword in the `aws redshift authorize-data-share` API\. This allows AWS Data Exchange to recognize the datashare in the service account and manage associating consumers to the datashare\.

     ```
     aws redshift authorize-data-share 
     --data-share-arn arn:aws:redshift:us-east-1:{PRODUCER_ACCOUNT}:datashare:{PRODUCER_CLUSTER_NAMESPACE}/salesshare 
     --consumer-identifier ADX
     ```

     You can use a conditional key `ConsumerIdentifier` for the `AuthorizeDataShare` and `DeauthorizeDataShare` APIs to explicitly allow or deny AWS Data Exchange to make calls to the two APIs in the IAM policy\.

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Sid": "VisualEditor0",
                 "Effect": "Deny",
                 "Action": [
                     "redshift:AuthorizeDataShare",
                     "redshift:DeauthorizeDataShare"
                 ],
                 "Resource": "*",
                 "Condition": {
                     "StringEqualsIgnoreCase": {
                         "redshift:ConsumerIdentifier": "ADX"
                     }
                 }
             }
         ]
     }
     ```
   + Use the Amazon Redshift console to authorize or remove authorization of AWS Data Exchange datashares\. For more information, see [Authorizing or removing authorization from datashares](authorize-datashare-console.md)\.
   + Optionally, you can implicitly authorize access to the AWS Data Exchange datashare when importing the datashare into an AWS Data Exchange dataset\.

   To remove authorization for access to the AWS Data Exchange datashares, use the `ADX` keyword in the `aws redshift deauthorize-data-share` API operation\. By doing this, you allow AWS Data Exchange to recognize the datashare in the service account and manage removing association from the datashare\.

   ```
   aws redshift deauthorize-data-share 
   --data-share-arn arn:aws:redshift:us-east-1:{PRODUCER_ACCOUNT}:datashare:{PRODUCER_CLUSTER_NAMESPACE}/salesshare 
   --consumer-identifier ADX
   ```

1. List datashares created in the cluster and look into the contents of the datashare\.

   The following example displays the information of a datashare named salesshare\. For more information, see [DESC DATASHARE](r_DESC_DATASHARE.md) and [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

   ```
   DESC DATASHARE salesshare;
                  
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

1. Drop datashares\. We recommend that you don't delete an AWS Data Exchange datashare shared to other AWS accounts using the DROP DATASHARE statement, those accounts will lose access to the datashare\. This action is irreversible\. This might breach data product offer terms in AWS Data Exchange\. If you want to delete an AWS Data Exchange datashare, see [DROP DATASHARE usage notes](r_DROP_DATASHARE.md#r_DROP_DATASHARE_usage)\.

   The following example drops a datashare named salesshare\.

   ```
   DROP DATASHARE salesshare;
   ERROR:  Drop of ADX-managed datashare salesshare requires session variable datashare_break_glass_session_var to be set to value '620c871f890c49'
   ```

   To allow dropping an AWS Data Exchange datashare, set the datashare\_break\_glass\_session\_var variable and run the DROP DATASHARE statement again\. If you want to delete an AWS Data Exchange datashare, see [DROP DATASHARE usage notes](r_DROP_DATASHARE.md#r_DROP_DATASHARE_usage)\.

   You can also use the Amazon Redshift console to delete datashares\. For more information, see [Deleting AWS Data Exchange datashares created in your account \(preview\)](delete-adx-datashare-console.md)\.

1. Use ALTER DATASHARE to remove objects from datashares at any point from the datashare\. Use REVOKE USAGE ON to revoke permissions on the datashare to certain consumers\. It revokes USAGE privileges on objects within a datashare and instantly stops access to all consumer clusters\. Listing datashares and the metadata queries, such as listing databases and tables, doesn't return the shared objects after access is revoked\.

   ```
   ALTER DATASHARE salesshare REMOVE TABLE public.tickit_sales_redshift;
   ```

   You can also use the Amazon Redshift console to edit datashares\. For more information, see [Editing AWS Data Exchange datashares \(preview\) ](edit-adx-datashare-console.md)\.

1. Grant or revoke GRANT USAGE from AWS Data Exchange datashares\. You can't grant or revoke GRANT USAGE for AWS Data Exchange datashare\. The following example shows an error when the GRANT USAGE privilege is granted to an AWS account for a datashare that AWS Data Exchange manages\.

   ```
   CREATE DATASHARE salesshare MANAGEDBY ADX;
   ```

   ```
   GRANT USAGE ON DATASHARE salesshare TO ACCOUNT '012345678910';
   ERROR:  Permission denied to add/remove consumer to/from datashare salesshare. Datashare consumers are managed by ADX.
   ```

   For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

**If you are a producer cluster administrator, follow these steps to create and publish a datashare product on the AWS Data Exchange console:**
+ When the AWS Data Exchange datashare has been created, the producer creates a new dataset, imports assets, creates a revision, and creates and publishes a new product\.

  Use the Amazon Redshift console to create datasets\. For more information, see [Creating data sets on AWS Data Exchange \(preview\)](create-dataset-console.md)\.

  For more information, see [Providing data products on AWS Data Exchange](https://https://docs.aws.amazon.com/data-exchange/latest/userguide/providing-data-sets.html)\.