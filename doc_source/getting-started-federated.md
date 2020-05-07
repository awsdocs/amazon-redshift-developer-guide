# Getting started with using federated queries<a name="getting-started-federated"></a>

To create a federated query, you follow this general approach: 

1. Set up connectivity from your Amazon Redshift cluster to your Amazon RDS or Aurora PostgreSQL DB instance\. 

   To do this, make sure that your RDS PostgreSQL or Aurora PostgreSQL DB instance can accept connections from your Amazon Redshift cluster\. We recommend that your Amazon Redshift cluster and Amazon RDS or Aurora PostgreSQL instance be in the same VPC and subnet group\. This way, you can add the security group for the Amazon Redshift cluster to the inbound rules of the security group for your RDS or Aurora PostgreSQL DB instance\. 

   You can also set up VPC peering or other networking that allows Amazon Redshift to make connections to your RDS or Aurora PostgreSQL instance\. For more information about VPC networking, see [Working with a DB instance in a VPC ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html) in the *Amazon RDS User Guide*\.

1. Set up secrets in AWS Secrets Manager for your RDS PostgreSQL and Aurora PostgreSQL databases\. Then reference the secrets in AWS Identity and Access Management \(IAM\) access policies and roles\. For more information, see [Creating a secret and an IAM role to use federated queries](federated-create-secret-iam-role.md)\. 
**Note**  
If your cluster uses enhanced VPC routing, you might need to configure an interface VPC endpoint for AWS Secrets Manager\. This is necessary when the VPC and subnet of your Amazon Redshift cluster don’t have access to the public AWS Secrets Manager endpoint\. When you use a VPC interface endpoint, communication between the Amazon Redshift cluster in your VPC and AWS Secrets Manager is routed privately from your VPC to the endpoint interface\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\. 

1. Apply the IAM role that you previously created to the Amazon Redshift cluster\. For more information, see [Creating a secret and an IAM role to use federated queries](federated-create-secret-iam-role.md)\.

1. Connect to your RDS PostgreSQL and Aurora PostgreSQL databases with an external schema\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\. For examples on how to use federated query, see [Example of using a federated query](federated_query_example.md)\.

1. Run your SQL queries referencing the external schema that references your RDS PostgreSQL and Aurora PostgreSQL databases\. 