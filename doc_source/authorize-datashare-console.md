# Authorizing or removing authorization from datashares<a name="authorize-datashare-console"></a>

As a producer cluster administrator, choose which data consumers to authorize to access datashares or to remove authorization from\. Authorized data consumers receive notifications to take actions on datashares\. If you are adding a cluster namespace as a data consumer, you don't have to perform authorization\.

Prerequisite: To authorize or remove authorization for the datashare, there must be at least one data consumer added to the datashare\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **DATASHARES**\. The datashare list page appears\.

1. Choose **In my account**\.

1. In the **Datashares in my account** section, do one of the following:
   + Choose one or more datashares that you want to authorize\. The Authorize data consumers page appears\. Then choose **Authorize**\.
   + Choose one or more datashares that you want to remove authorization\. The Remove authorization page appears\. Then choose **Remove authorization**\.

After data consumers are authorized, they can access datashare objects and create a consumer database to query the data\. 

After authorization is removed, data consumers will lose access to the datashare immediately\.