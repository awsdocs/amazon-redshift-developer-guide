# Producer cluster administrator actions<a name="producer-cluster-admin"></a>

**If you are a producer cluster administrator or database owner** – follow these steps:

1. Create datashares in your cluster and add datashare objects to the datashares\. For more detailed steps on how to create datashares and add datashare objects to datashares, see [Sharing data within an AWS account](within-account.md)\. For information about the CREATE DATASHARE and ALTER DATASHARE, see [CREATE DATASHARE](r_CREATE_DATASHARE.md) and [ALTER DATASHARE](r_ALTER_DATASHARE.md)\.

   The following example adds different datashare objects to the datashare `salesshare`\.

   ```
   -- Add schema to datashare
   ALTER DATASHARE salesshare ADD SCHEMA PUBLIC;
   
   -- Add table under schema to datashare
   ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;
   
   -- Add view to datashare 
   ALTER DATASHARE salesshare ADD TABLE public.sales_data_summary_view;
   
   -- Add all existing tables and views under schema to datashare (does not include future table)
   ALTER DATASHARE salesshare ADD ALL TABLES in schema public;
   ```

   You can also use the Amazon Redshift console to create or edit datashares\. For more information, see [Creating datashares](create-datashare-console.md) and [Editing datashares created in your account](edit-datashare-console.md)\.

1. Delegate permissions to operate on the datashare\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   The following example grants permissions to `dbuser` on `salesshare`\.

   ```
   GRANT ALTER, SHARE ON DATASHARE salesshare TO dbuser;
   ```

   Cluster superusers and the owners of the datashare can grant or revoke modification permissions on the datashare to additional users\.

1. Add consumers to or remove consumers from datashares\. The following example adds the AWS account ID to `salesshare`\. For more information, see [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md)\.

   ```
   GRANT USAGE ON DATASHARE salesshare TO ACCOUNT '123456789012';
   ```

   You can only grant permissions to one data consumer in a GRANT statement\.

   Cluster superusers and the owners of datashare objects or users that have SHARE privilege on the datashare can add consumers to or remove consumers from a datashare\. To do so, they use GRANT USAGE or REVOKE USAGE\.

   You can also use the Amazon Redshift console to add or remove data consumers for datashares\. For more information, see [Adding data consumers to datashares](add-data-consumer-console.md) and [Removing data consumers from datashares](remove-data-consumer-console.md)\.

1. \(Optional\) Revoke access to the datashare from AWS accounts if you don't want to share the data with the consumers anymore\.

   ```
   REVOKE USAGE ON DATASHARE salesshare FROM ACCOUNT '123456789012';
   ```

**If you are a producer account administrator** – follow these steps:

After granting usage to the AWS account, the datashare status is `pending_authorization`\. The producer account administrator should authorize datashares using the Amazon Redshift console and choose the data consumers\.

Sign in to the [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\. Then choose which data consumers to authorize to access datashares or to remove authorization from\. Authorized data consumers receive notifications to take actions on datashares\. If you are adding a cluster namespace as a data consumer, you don't have to perform authorization\. After data consumers are authorized, they can access datashare objects and create a consumer database to query the data\. For more information, see [Authorizing or removing authorization from datashares](authorize-datashare-console.md)\.