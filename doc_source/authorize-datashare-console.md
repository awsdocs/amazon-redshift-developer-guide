# Authorizing or removing authorization from datashares<a name="authorize-datashare-console"></a>

As a producer cluster administrator, choose which data consumers to authorize to access datashares or to remove authorization from\. Authorized data consumers receive notifications to take actions on datashares\. If you are adding a cluster namespace as a data consumer, you don't have to perform authorization\.

Prerequisite: To authorize or remove authorization for the datashare, there must be at least one data consumer added to the datashare\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **DATASHARES**\. The datashare list page appears\.

1. Choose **In my account**\.

1. In the **Datashares in my account** section, do one of the following:
   + Choose one or more datashares that you want to authorize\. The Authorize data consumers page appears\. Then choose **Authorize**\.

     For AWS Data Exchange datashare \(preview\), you can only authorize one datashare at a time\.

     When you authorize an AWS Data Exchange datashare, you are sharing the datashare with the AWS Data Exchange service and allowing AWS Data Exchange to manage access to the datashare on your behalf\. AWS Data Exchange allows access to consumers by adding consumer accounts as data consumers to the AWS Data Exchange datashare when they subscribe to the products\. AWS Data Exchange doesn't have read access to the datashare\.
   + Choose one or more datashares that you want to remove authorization\. The Remove authorization page appears\. Then choose **Remove authorization**\.

After data consumers are authorized, they can access datashare objects and create a consumer database to query the data\. 

After authorization is removed, data consumers lose access to the datashare immediately\.