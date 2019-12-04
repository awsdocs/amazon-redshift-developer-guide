# Federated Query in Amazon Redshift \(Preview\)<a name="federated-overview"></a>


|  | 
| --- |
| This is prerelease documentation for the federated query feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

You can query data in Amazon RDS for PostgreSQL and Amazon Aurora with PostgreSQL compatibility from Amazon Redshift using external schemas\. This type of query is called a *federated query*\. You can use this capability to combine the data queried from one or more Amazon RDS PostgreSQL and Amazon Aurora PostgreSQL databases with data already in Amazon Redshift\. You can also combine such data with data in Amazon S3 tables\. 

Amazon Redshift exposes RDS PostgreSQL and Aurora PostgreSQL metadata with the information schema \(`information_schema`\) in Amazon Redshift\. Doing this enables business intelligence \(BI\) tools, such as Tableau and Micro\-strategy to view data referenced by the external schema\.

An Amazon Redshift external schema references a database in an external RDS PostgreSQL or Aurora PostgreSQL\. When it does so, these limitations apply: 
+ When creating an external schema referencing Aurora, the Aurora PostgreSQL database must be at version 9\.6, 10\.4, 10\.5, 10\.6, or 10\.7\. 
+ When creating an external schema referencing Amazon RDS, the Amazon RDS PostgreSQL database must be at version 9\.3 to 9\.6, or 10\.1 to 10\.9, or 11\.0 to 11\.4, or 12\. 

To create a federated query, you follow this general workflow: 

1. Set up your RDS PostgreSQL and Aurora PostgreSQL instances in the same VPC as your Amazon Redshift clusters\.

1. Create an instance in RDS PostgreSQL or Aurora PostgreSQL in the same VPC as your Amazon Redshift cluster\. If the instance is publicly accessible, configure its security group's inbound rule to: `Type: PostgreSQL, Protocol: TCP, Port Range: 5432, Source: 0.0.0.0/0`\. Otherwise, if the instance is not publicly accessible, you don't need to configure an inbound rule\. 

1. Set up secrets in AWS Secrets Manager for your RDS PostgreSQL and Aurora PostgreSQL databases\. Then reference the secrets in AWS Identity and Access Management \(IAM\) access policies and roles\. For more information, see [Create a Secret and an IAM Role for Federated Query](federated-create-secret-iam-role.md)\. 

1. Create an Amazon Redshift cluster\. When you create the cluster, switch off **Use defaults**\. Then in the **Network and security** section, choose **Cluster subnet group**, and choose a subnet group in your VPC that is set up for RDS PostgreSQL or Aurora PostgreSQL\. For information about how to set up your subnet, see [Working with a DB Instance in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html) in the *Amazon RDS User Guide*\. 

   The cluster you use to work with the preview can be a new cluster or a cluster restored from a snapshot created from a cluster with a cluster maintenance version no later than `1.0.11420`\. 

   To use federated query, you must confirm that your Amazon Redshift cluster is using the cluster maintenance track for **Preview** with the track named **preview\_features**\. The current cluster maintenance version for the preview track is `1.0.11532`\. For more information, see [Choosing Cluster Maintenance Tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks) in the *Amazon Redshift Cluster Management Guide*\. For more information about creating a cluster, see [Creating a Cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the *Amazon Redshift Cluster Management Guide*\. 

1. Apply the IAM role you previously created to the Amazon Redshift cluster\. For more information, see [Create a Secret and an IAM Role for Federated Query](federated-create-secret-iam-role.md)\.

1. Connect to your RDS PostgreSQL and Aurora PostgreSQL databases with an external schema\. For more information, see [CREATE EXTERNAL SCHEMA](federated-external-schema.md)\. 

1. Run your SQL queries referencing the external schema that references RDS PostgreSQL and Aurora PostgreSQL databases\. Amazon Redshift intelligently pushes down the execution of predicate and aggregate functions to RDS PostgreSQL and Aurora PostgreSQL\. 

**Note**  
When working with the preview:  
Amazon Redshift clusters must be on a **Preview** maintenance track\.
Use the new Amazon Redshift console\. 
The federated query preview period is expected to run until February 15, 2020\. Although, it might be extended\. 
For any questions, issues, or feedback related to the preview features during the preview period, email `redshiftpreviews@amazon.com` or open a support case with Amazon Support\. 

**Topics**
+ [Create a Secret and an IAM Role for Federated Query](federated-create-secret-iam-role.md)
+ [CREATE EXTERNAL SCHEMA](federated-external-schema.md)
+ [Example Using Federated Query](federated_query_example.md)
+ [Data Type Differences Between Amazon Redshift and Supported RDS PostgreSQL or Aurora PostgreSQL Databases](federated-data-types.md)
+ [Limitations and Considerations When Accessing Federated Data with Amazon Redshift](federated-limitations.md)