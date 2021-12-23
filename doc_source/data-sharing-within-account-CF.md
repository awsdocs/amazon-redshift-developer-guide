# Getting started data sharing with AWS CloudFormation<a name="data-sharing-within-account-CF"></a>

You can automate data sharing setup by using an AWS CloudFormation stack, which provisions AWS resources\. The CloudFormation stack described following sets up data sharing between two Amazon Redshift clusters in the same AWS account\. Thus, you can get up and running quickly without having to run SQL statements to provision your resources\.

The stack creates a datashare on the cluster that you designate\. The datashare includes a table and sample read\-only data\. This data can be read by another Redshift cluster\.

If instead you want to start sharing data in an AWS account by running SQL statements to set up a datashare and grant permissions, without using CloudFormation, see [Sharing data within an AWS account](within-account.md)\.

Before running the data sharing CloudFormation stack, you must be logged in with a user that has permission to create an IAM role and a Lambda function\. You also need two Amazon Redshift clusters in the same account\. You use one, the *producer*, to share the sample data, and the other, the *consumer*, to read it\. The primary requirement for these clusters is that each use RA3 nodes\. For additional requirements, see [Data sharing considerations in Amazon Redshift](considerations.md)\.

For more information about getting started setting up an Amazon Redshift cluster, see [Getting started with Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html)\. For more information about automating setup with CloudFormation, see [What is AWS CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)\.

## <a name="data-sharing-within-account-CF-stack"></a>

Use the following procedure to launch your CloudFormation stack for Amazon Redshift data sharing\. Before doing so, make sure you have two Amazon Redshift clusters in the same account\. 

**To launch your CloudFormation stack for Amazon Redshift data sharing:**

1. Click [https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=DataShare&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/DataShare.yml](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=DataShare&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/DataShare.yml), which takes you to the CloudFormation service in the AWS Management Console\.

   If you are prompted, sign in\. 

   The stack creation process starts, referencing a CloudFormation template file, which is stored in Amazon S3\. A CloudFormation *template* is a text file in JSON format that declares AWS resources that make up a stack\. 

1. Choose **Next** to enter the stack details\.

1. Under **Parameters**, for each cluster, enter the following:
   + The Amazon Redshift cluster name, for example **ra3\-consumer\-cluster**
   + A specific database name, for example **dev**
   + The name of a database user, for example **consumeruser**

   We recommend using test clusters, because the stack creates several database objects\.

   Choose **Next**\. 

   The stack options appear\. 

1. Choose **Next** to accept the default settings\.

1. Under **Capabilities**, choose **I acknowledge that AWS CloudFormation might create IAM resources\.**

1. Choose **Create stack**\. 

CloudFormation takes about ten minutes to build the Amazon Redshift stack using the template, creating a datashare called `myproducer_share`\. The stack creates the datashare in the database specified in the stack details\. Only objects from that database can be shared\.

If an error occurs while the stack is created, do the following:
+ Make sure that you entered the correct cluster name, database name, and database user name for each Redshift cluster\. 
+ Make sure that your cluster has RA3 nodes\.

## Querying the datashare that you created<a name="data-sharing-within-account-CF-querying"></a>

To use the following procedure, make sure that you have the required permissions for running queries on each cluster described\.

**To query your datashare:**

1. Connect to the producer cluster on the database entered when your CloudFormation stack was created, using a client tool such as the Redshift query editor\.

1. Query for datashares\.

   ```
   SHOW DATASHARES;
   ```

   The preceding command returns the name of the datashare created by the stack, called `myproducer_share`\. It also returns the name of the database associated with the datashare,` myconsumer_db`\. 

   Copy the producer namespace identifier to use in a later step\.

1. Describe objects in the datashare\.

   ```
   DESC DATASHARE myproducer_share;
   ```

   When you describe the datashare, it returns properties for tables and views\. The stack adds tables and views with sample data to the producer database, for example `tickit_sales` and `tickit_sales_view`\. For more information about the Redshift sample data, see [Sample database](https://docs.aws.amazon.com/redshift/latest/dg/c_sampledb.html)\.

   You don't have to delegate permissions on the datashare to run queries\. The stack grants the necessary permissions\.

1. Connect to the consumer cluster using your client tool\. Describe the datashare, specifying the producer's namespace\.

   ```
   DESC DATASHARE myproducer_share OF NAMESPACE '<namespace id>'; --specify the unique identifier for the producer namespace
   ```

1. You can query tables in the datashare by specifying the datashare's database and schema\.

   ```
   SELECT * FROM myconsumer_db.myproducer_schema.tickit_sales_view;
   ```

   The query runs against the view in the shared schema\. You can't connect directly to databases created from datashares\. They are read\-only\.

1. Run a query that includes aggregations\.

   ```
   SELECT * FROM myconsumer_db.myproducer_schema.tickit_sales ORDER BY 1,2 LIMIT 5;
   ```

   The query returns sales and seller data from the sample Tickit data\.

   For more examples of datashare queries, see [Sharing data within an AWS account](within-account.md)\.