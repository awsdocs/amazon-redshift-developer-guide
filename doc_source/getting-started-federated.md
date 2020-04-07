# Getting Started with Using Federated Query<a name="getting-started-federated"></a>

To create a federated query, you follow this general approach: 

1. Set up connectivity from your Amazon Redshift cluster to your Amazon RDS or Aurora PostgreSQL DB instance\. 

   To do this, make sure that your RDS PostgreSQL or Aurora PostgreSQL DB instance can accept connections from your Amazon Redshift cluster\. We recommend that your Amazon Redshift cluster and Amazon RDS or Aurora PostgreSQL instance be in the same VPC and subnet group\. This way, you can add the security group for the Amazon Redshift cluster to the inbound rules of the security group for your RDS or Aurora PostgreSQL DB instance\. 

   You can also set up VPC peering or other networking that allows Amazon Redshift to make connections to your RDS or Aurora PostgreSQL instance\. For more information about VPC networking, see [Working with a DB Instance in a VPC ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html) in the *Amazon RDS User Guide*\.

1. Set up secrets in AWS Secrets Manager for your RDS PostgreSQL and Aurora PostgreSQL databases\. Then reference the secrets in AWS Identity and Access Management \(IAM\) access policies and roles\. For more information, see [Creating a Secret and an IAM Role to Use Federated Queries](federated-create-secret-iam-role.md)\. 

1. Apply the IAM role that you previously created to the Amazon Redshift cluster\. For more information, see [Creating a Secret and an IAM Role to Use Federated Queries](federated-create-secret-iam-role.md)\.

1. Connect to your RDS PostgreSQL and Aurora PostgreSQL databases with an external schema\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\. For examples on how to use federated query, see [Example of Using a Federated Query](federated_query_example.md)\.

1. Run your SQL queries referencing the external schema that references your RDS PostgreSQL and Aurora PostgreSQL databases\. 