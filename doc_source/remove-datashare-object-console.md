# Removing datashare objects from datashares<a name="remove-datashare-object-console"></a>

Remove one or more objects from a datashare\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **CLUSTERS**, then choose your cluster\. The cluster details page appears\.

1. Choose **Datashares**\.

1. In the **Datashares created in my account** section, choose **Connect to database**\. For more information, see [Connecting to a database ](connect-database-console.md)\.

1. Choose the datashare you want to edit, then choose **Edit**\. The datashare details page appears\.

1. To remove one or more datashare objects to the datashare, do one of the following:
   + To remove schemas from the datashare, choose one or more schemas\. Then choose **Remove**\. Amazon Redshift removes the specified schemas and all the objects of the specified schemas from the datashare\.
   + To remove tables and views from the datashare, choose one or more tables and views\. Then choose **Remove**\. Alternatively, choose **Remove by schema** to remove all tables and views in the specified schemas\.
   + To remove user\-defined functions from the datashare, choose one or more user\-defined functions\. Then choose **Remove**\. Alternatively, choose **Remove by schema** to remove all user\-defined functions in the specified schemas\.