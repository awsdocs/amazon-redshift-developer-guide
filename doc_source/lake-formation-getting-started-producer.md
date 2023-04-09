# Working with Lake Formation\-managed datashares as a producer<a name="lake-formation-getting-started-producer"></a>

As a producer cluster administrator, follow these steps to share datashares to Lake Formation:

1. Create datashares in your cluster and authorize AWS Lake Formation to access the datashares\.

   Only cluster superuser and database owners can create datashares\. Each datashare is associated with a database during creation\. Only objects from that database can be shared in that datashare\. Multiple datashares can be created on the same database with the same or different granularity of objects\. There is no limit on the number of datashares you can create on a cluster\.

   ```
   CREATE DATASHARE salesshare;
   ```

1.  Add objects to the datashare\. The producer cluster administrator continues to manage datashare objects that are available\. To add objects to a datashare, add the schema before adding objects\. When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\. For more information, see [ALTER DATASHARE](https://docs.aws.amazon.com/redshift/latest/dg/r_ALTER_DATASHARE.html)\. 

   ```
   ALTER DATASHARE salesshare ADD SCHEMA PUBLIC;
   ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;
   ALTER DATASHARE salesshare ADD ALL TABLES IN SCHEMA PUBLIC;
   ```

   You can also add views to a datashare\. Supported views are standard views, late binding views, and materialized views\.

   ```
   CREATE VIEW public.sales_data_summary_view AS SELECT * FROM public.tickit_sales_redshift;
   ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;
   ```

   Use ALTER DATASHARE to share schemas, tables, and views, in a given schema\. Superusers, datashare owners, or users who have ALTER or ALL permissions on the datashare can alter the datashare to add objects to or remove objects from it\. Database users should be the owners of the objects or have SELECT, USAGE, or ALL permissions on the objects\. 

   Use the INCLUDENEW clause to add any new tables and views created in a specified schema to the datashare\. Only superusers can change this property for each datashare\-schema pair\.

   ```
   ALTER DATASHARE salesshare ADD SCHEMA PUBLIC;
   ALTER DATASHARE salesshare SET INCLUDENEW = TRUE FOR SCHEMA PUBLIC;
   ```

1. Grant access of the datashare to a Lake Formation administrator account\.

   ```
   GRANT USAGE ON DATASHARE salesshare TO ACCOUNT '012345678910' VIA DATA CATALOG;
   ```

   To revoke usage, use the following command\.

   ```
   REVOKE USAGE ON DATASHARE salesshare FROM ACCOUNT '012345678910' VIA DATA CATALOG;
   ```

1. Authorize access to the datashare for Lake Formation by using the `aws redshift authorize-data-share` API operation\. This lets Lake Formation recognize the datashare in the service account and manage associating consumers to the datashare\.

   ```
   aws redshift authorize-data-share 
   --data-share-arn arn:aws:redshift:us-east-1:{PRODUCER_ACCOUNT}:datashare:{PRODUCER_CLUSTER_NAMESPACE}/salesshare 
   --consumer-identifier {"DataCatalog/<consumer-account-id>"}
   ```

    To remove authorization from Lake Formation\-managed datashares, use the `aws redshift deauthorize-data-share` API operation\. By doing so, you allow AWS Lake Formation to recognize the datashare in the service account and remove authorization\. 

   ```
   aws redshift deauthorize-data-share 
   --data-share-arn arn:aws:redshift:us-east-1:{PRODUCER_ACCOUNT}:datashare:{PRODUCER_CLUSTER_NAMESPACE}/salesshare 
   --consumer-identifier {"DataCatalog/<consumer-account-id>"}
   ```

    At any time, if the producer cluster administrator decides that there is no longer a need to share data with the consumer cluster, they can use DROP DATASHARE to delete the datashare\. The associated permissions and objects in Lake Formation are not automatically deleted\. 

   ```
   DROP DATASHARE salesshare;
   ```

    After authorizing the Lake Formation account to manage the datashare, the Lake Formation administrator can discover the shared datashare and create a database in the AWS Glue Data Catalog linking to the datashare\. The administrator must also create local resources that define how objects within the datashare should map to objects within Lake Formation\. For more information about discovering datashares and creating local resources, see Accepting a datashare invitation from Redshift\. 