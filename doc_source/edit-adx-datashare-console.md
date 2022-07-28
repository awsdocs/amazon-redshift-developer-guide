# Editing AWS Data Exchange datashares<a name="edit-adx-datashare-console"></a>

Edit AWS Data Exchange datashares using the console\. Connect to a database first to see the list of datashares created in your account\.

For AWS Data Exchange datashares, you can't make changes to data consumers\.

To edit the publicly accessible setting for AWS Data Exchange datashares, use the Query editor v2\. Amazon Redshift generates a random one\-time value to set the session variable to allow turning this setting off\. For more information, see [ALTER DATASHARE usage notes](r_ALTER_DATASHARE.md#r_ALTER_DATASHARE_usage)\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Clusters**, then choose your cluster\. The cluster details page appears\.

1. From the navigator menu, choose **Editor**, then **Query editor v2**\.

1. If this is the first time you use the Query editor v2, configure your AWS account\. By default, an AWS owned key is used to encrypt resources\. For more information about configuring your AWS account, see [Configuring your AWS account](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-getting-started.html) in the *Amazon Redshift Cluster Management Guide*\.

1. To connect to the cluster that your AWS Data Exchange datashare is in, choose **Database** and the cluster name in the tree\-view panel\. If prompted, enter the connection parameters\.

1. Copy the following SQL statement\. The following example changes the publicly accessible setting of the salesshare datashare\.

   ```
   ALTER DATASHARE salesshare SET PUBLICACCESSIBLE FALSE;
   ```

1. To run the copied SQL statement, choose **Queries** and paste the copied SQL statement in the query area\. Then choose **Run**\.

   An error appears following:

   ```
   ALTER DATASHARE salesshare SET PUBLICACCESSIBLE FALSE;
   ERROR:  Alter of ADX-managed datashare salesshare requires session variable datashare_break_glass_session_var to be set to value 'c670ba4db22f4b'
   ```

   The value 'c670ba4db22f4b' is a random one\-time value that Amazon Redshift generates when an unrecommended operation occurs\.

1. Copy and paste the following sample statement into the query area\. Then run the command\. The `SET datashare_break_glass_session_var` command applies a permission to allow an unrecommended operation for an AWS Data Exchange datashare\.

   ```
   SET datashare_break_glass_session_var to 'c670ba4db22f4b';
   ```

1. Run the ALTER DATASHARE statement again\.

   ```
   ALTER DATASHARE salesshare;
   ```

Amazon Redshift updates your datashare with the changes\.