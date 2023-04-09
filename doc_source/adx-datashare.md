# Working with AWS Data Exchange for Amazon Redshift<a name="adx-datashare"></a>

*AWS Data Exchange for Amazon Redshift* makes it convenient to license access to your Amazon Redshift data through AWS Data Exchange\. When a customer subscribes to a product with AWS Data Exchange datashares, AWS Data Exchange automatically adds the customer as a data consumer on all AWS Data Exchange datashares included with the product\. Invoices are automatically generated, and payments are centrally collected and automatically disbursed through AWS Marketplace Entitlement Service\.

Providers can license data in Amazon Redshift at a granular level, such as schemas, tables, views, and user\-defined functions\. You can use the same AWS Data Exchange datashare across multiple AWS Data Exchange products\. Any objects added to the AWS Data Exchange datashare is available to consumers\. Producers can view all AWS Data Exchange datashares managed by AWS Data Exchange on their behalf using Amazon Redshift API operations, SQL commands, and the Amazon Redshift console\. Customers who subscribe to a product AWS Data Exchange datashares have read\-only access to the objects in the datashares\. 

Customers who want to consume third\-party producer data can browse the AWS Data Exchange catalog to discover and subscribe to datasets in Amazon Redshift\. After their AWS Data Exchange subscription is active, they can create a database from the datashare in their cluster and query the data in Amazon Redshift\.

## How AWS Data Exchange datashares work<a name="adx-how-it-works"></a>

### Managing AWS Data Exchange datashares as a producer administrator<a name="provider-how-it-works"></a>

 If you are a data producer \(also known as a provider on AWS Data Exchange\), you can create AWS Data Exchange datashares that connect to your Amazon Redshift databases\. To add AWS Data Exchange datashares to products on AWS Data Exchange, you must be a registered AWS Data Exchange provider\.

For more information on how to get started with AWS Data Exchange datashares, see [Sharing licensed Amazon Redshift data on AWS Data Exchange](adx-getting-started.md)\.

### Using AWS Data Exchange datashares as a consumer with an active AWS Data Exchange subscription<a name="subscriber-how-it-works"></a>

If you are a consumer with an active AWS Data Exchange subscription \(also known as a subscriber on AWS Data Exchange\), you can browse the AWS Data Exchange catalog on the AWS Data Exchange console to discover products containing AWS Data Exchange datashares\. 

After you subscribe to a product that contains AWS Data Exchange datashares, create a database from the datashare within your cluster\. You can then query the data in Amazon Redshift directly without extracting, transforming, and loading the data\. 

For more information on how to get started with AWS Data Exchange datashares, see [Sharing licensed Amazon Redshift data on AWS Data Exchange](adx-getting-started.md)\.

## Considerations when using AWS Data Exchange for Amazon Redshift<a name="adx-usage-notes"></a>

When using AWS Data Exchange for Amazon Redshift, consider the following:
+ Both producers and consumers must use the RA3 instance types to use Amazon Redshift datashares\. Producers must use the RA3 instance types with the latest Amazon Redshift cluster version\.
+ Both the producer and consumer clusters must be encrypted\.
+ You must be registered as an AWS Data Exchange provider to list products on AWS Data Exchange, including products that contain AWS Data Exchange datashares\. For more information, see [Getting started as a provider](https://docs.aws.amazon.com/data-exchange/latest/userguide/provider-getting-started.html)\.
+ You don't need to be a registered AWS Data Exchange provider to find, subscribe to, and query Amazon Redshift data through AWS Data Exchange\.
+ To control access to your data, create AWS Data Exchange datashares with the publicly accessible setting turned on\. To alter an AWS Data Exchange datashare to turn off the publicly accessible setting, set the session variable to allow ALTER DATASHARE SET PUBLICACCESSIBLE FALSE\. For more information, see [ALTER DATASHARE usage notes](r_ALTER_DATASHARE.md#r_ALTER_DATASHARE_usage)\.
+ Producers can't manually add or remove consumers from AWS Data Exchange datashares because access to the datashares is granted based on having an active subscription to an AWS Data Exchange product that contains the AWS Data Exchange datashare\.
+ Producers can't view the SQL queries that consumers run\. They can only view metadata, such as the number of queries or the objects consumers query, through Amazon Redshift tables that only the producer can access\. For more information, see [Tracking usage and auditing in data sharing in Amazon Redshift](auditing.md)\.
+ We recommend that you make your datashares publicly accessible\. If you don't, subscribers on AWS Data Exchange with publicly accessible consumer clusters won't be able to use your datashare\.
+ We recommend that you don't delete an AWS Data Exchange datashare shared to other AWS accounts using the DROP DATASHARE statement\. If you do, the AWS accounts that have access to the datashare will lose access\. This action is irreversible\. Performing this type of alteration can breach data product terms in AWS Data Exchange\. If you want to delete an AWS Data Exchange datashare, see [DROP DATASHARE usage notes](r_DROP_DATASHARE.md#r_DROP_DATASHARE_usage)\.
+ For cross\-Region data sharing, you can create AWS Data Exchange datashares to share licensed data\.