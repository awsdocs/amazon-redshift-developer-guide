# Getting started using federated queries to PostgreSQL with AWS CloudFormation<a name="getting-started-federated-CF"></a>

You can use federated queries to query across operational databases\. In this getting\-started guide, you can automate setup by using a sample AWS CloudFormation stack to enable a federated query from an Amazon Redshift cluster to an Aurora PostgreSQL serverless database\. You can get up and running quickly without having to run SQL statements to provision your resources\.

The stack creates an external schema, referencing your Aurora PostgreSQL instance, which includes tables with sample data\. You can query tables in the external schema from your Redshift cluster\.

If instead you want to get started with federated queries by running SQL statements to set up an external schema, without using CloudFormation, see [Getting started with using federated queries to PostgreSQL](getting-started-federated.md)\.

Before running the CloudFormation stack for federated queries, make sure that you have an Amazon Aurora PostgreSQL\-Compatible Edition serverless database with the Data API turned on\. You can turn on the Data API in the database properties\. If you can't find the setting, double\-check that you are running a serverless instance of Aurora PostgreSQL\. Also make sure that you have a Amazon Redshift cluster that uses RA3 nodes\. We recommend that both the Redshift cluster and serverless Aurora PostgreSQL instance are in the same virtual private cloud \(VPC\) and subnet group\. This way, you can add the security group for the Amazon Redshift cluster to the inbound rules of the security group for your Aurora PostgreSQL database instance\.

For more information about getting started setting up an Amazon Redshift cluster, see [Getting started with Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html)\. For more information about setting up resources with CloudFormation, see [What is AWS CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)\. For more information about setting up an Aurora database, see [Creating an Aurora Serverless v1 DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.create.html)\.

## Launching a CloudFormation stack for Redshift federated queries<a name="getting-started-federated-CF-stack"></a>

Use the following procedure to launch your CloudFormation stack for Amazon Redshift to enable federated queries\. Before doing so, make sure you have your Amazon Redshift cluster and your serverless Aurora PostgreSQL instance set up\.

**To launch your CloudFormation stack for federated queries**

1. Click [https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=FederatedQuery&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/FederatedQuery.yml](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=FederatedQuery&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/FederatedQuery.yml) here to launch the CloudFormation service in the AWS Management Console\.  

   If you are prompted, sign in\.

   The stack creation process starts, referencing a CloudFormation template file, which is stored in Amazon S3\. A CloudFormation *template* is a text file in JSON format that declares AWS resources that make up a stack\. 

1. Choose **Next** to enter the stack details\.

1. Under **Parameters**, for the cluster, enter the following:
   + The Amazon Redshift cluster name, for example **ra3\-consumer\-cluster**
   + A specific database name, for example **dev**
   + The name of a database user, for example **consumeruser**

   Also enter the parameters for the Aurora database, including the user, database name, port, and endpoint\. We recommend using a test cluster and test serverless database, because the stack creates several database objects\.

   Choose **Next**\. 

   The stack options appear\. 

1. Choose **Next** to accept the default settings\.

1. Under **Capabilities**, choose **I acknowledge that AWS CloudFormation might create IAM resources\.**

1. Choose **Create stack**\. 

Choose **Create stack**\. CloudFormation provisions the template resources, which takes about 10 minutes, and creates an external schema\.

If an error occurs while the stack is created, do the following:
+ View the CloudFormation **Events** tab for information that can help you resolve the error\.
+ Make sure that you entered the correct name, database name, and database user name for the Redshift cluster\. Also check the parameters for the Aurora PostgreSQL instance\.
+ Make sure that your cluster has RA3 nodes\.
+ Make sure that your database and Redshift cluster are in the same subnet and security group\.

## Querying data from the external schema<a name="getting-started-federated-CF-stack-query"></a>

To use the following procedure, make sure that you have the required permissions for running queries on the cluster and the database described\.

**To query an external database with federated query**

1. Connect to the Redshift database that you entered when you created the stack, using a client tool such as the Redshift query editor\. 

1. Query for the external schema created by the stack\.

   ```
   select * from svv_external_schemas;
   ```

   The [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md) view returns information about available external schemas\. In this case, the external schema created by the stack is returned, `myfederated_schema`\. You might also have other external schemas returned, if you have any set up\. The view also returns the schema's associated database\. The database is the Aurora database that you entered when you created the stack\. The stack adds a table to the Aurora database, called `category`, and another table called `sales`\.

1. Run SQL queries on tables in the external schema that references your Aurora PostgreSQL database\. The following example shows a query\.

   ```
   SELECT count(*) FROM myfederated_schema.category;
   ```

   The `category` table returns several records\. You can also return records from the `sales` table\.

   ```
   SELECT count(*) FROM myfederated_schema.sales;
   ```

   For more examples, see [Examples of using a federated query](federated_query_example.md)\.