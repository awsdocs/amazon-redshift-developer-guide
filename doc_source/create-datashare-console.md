# Creating datashares<a name="create-datashare-console"></a>

As a producer cluster administrator, you can create datashares from the **Databases** or **Datashares** tabs in the cluster details page\.

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Clusters**, then choose your cluster\. The cluster details page appears\.

1. In the cluster details page, do one of the following:
   + From the **Databases** tab, in the **Database** section, choose a database\. The database details page appears\.

     Choose **Create datashare**\. You can only create a datashare from a local database\. If you haven't connected to the database, the Connect to database page appears\. Follow the steps in [Connecting to a database](connect-database-console.md) to connect to a database\. If there is a recent connection, the Create datashare page appears\. 
   + From the **Datashares** tab, in the **Datashares** section, connect to a database if you don't have a database connection\.

     In the **Datashares created in my cluster** section, choose **Create datashare**\. The Create datashare page appears\.

1. In the **Datashare information** section, choose one of the following:
   + Choose **Datashare** to create datashares to share data for read purpose across different Amazon Redshift clusters or in the same AWS account or different AWS accounts\.
   + Choose **AWS Data Exchange datashare** to create datashares to license your data through AWS Data Exchange\.

1. Specify values for **Datashare name**, **Database name**, and **Publicly accessible**\.

   When you change the database name, make a new database connection\.

1. In the **Datashare objects** section, to add objects to a datashare, follow [Adding datashare objects to datashares](add-datashare-object-console.md)\.

1. In the **Data consumers** section, you can choose to publish to a Redshift account, or publish to the AWS Glue Data Catalog, which starts the process of sharing data via Lake Formation\. Publishing your datashare to Redshift accounts means sharing your data with another Redshift account that acts as the consumer cluster\.
**Note**  
Once the datashare is created, you cannot edit the configuration to publish to the other option\.

1. Choose **Create datashare**\.

Amazon Redshift creates the datashare\. After the datashare is created, you can create databases from the datashare\.