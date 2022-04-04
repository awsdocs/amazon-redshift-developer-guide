# Deleting AWS Data Exchange datashares created in your account \(preview\)<a name="delete-adx-datashare-console"></a>


|  | 
| --- |
| This is prerelease documentation for the AWS Data Exchange for Amazon Redshift feature, which is in preview release\. The documentation and the feature are both subject to change\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to adx\-redshift\-preview@amazon\.com\.   | 

Delete AWS Data Exchange datashares created in your account using the console\. Connect to a database first to see the list of datashares created in your account\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **CLUSTERS**, then choose your cluster\. The cluster details page appears\.

1. From the navigator menu, choose **Editor**, then **Query editor v2**\.

1. If this is the first time you use the Query editor v2, configure your AWS account\. By default, an AWS owned key is used to encrypt resources\. For more information about configuring your AWS account, see [Configuring your AWS account](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-getting-started.html) in the *Amazon Redshift Cluster Management Guide*\.

1. To connect to the cluster that your AWS Data Exchange datashare is in, choose **Database** and the cluster name in the tree\-view panel\. If prompted, enter the connection parameters\.

1. Copy the following SQL statement\. The following example drops the salesshare datashare\.

   ```
   DROP DATASHARE salesshare
   ```

1. To run the copied SQL statement, choose **Queries** and paste the copied SQL statement in the query area\. Then choose **Run**\.

   An error appears following:

   ```
   ERROR:  Drop of ADX-managed datashare salesshare requires session variable datashare_break_glass_session_var to be set to value '620c871f890c49'
   ```

   The value '620c871f890c49' is a random one\-time value that Amazon Redshift generates when an unrecommended operation occurs\.

1. Copy and paste the following sample statement into the query area\. Then run the command\. The `SET datashare_break_glass_session_var` command applies a permission to allow an unrecommended operation for an AWS Data Exchange datashare\.

   ```
   SET datashare_break_glass_session_var to '620c871f890c49';
   ```

1. Run the DROP DATASHARE statement again\.

   ```
   DROP DATASHARE salesshare;
   ```

After the datashare is deleted, datashare consumers lose access to the datashare\. 

Deleting a shared AWS Data Exchange datashare can breach data product terms in AWS Data Exchange\.