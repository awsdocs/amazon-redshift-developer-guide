# Create a Secret and an IAM Role for Federated Query<a name="federated-create-secret-iam-role"></a>


|  | 
| --- |
| This is prerelease documentation for the federated query feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

The following steps show how to create a secret and an IAM role to use with federated query\. 

## Prerequisites<a name="federated-create-secret-prerequisites"></a>

You must have the following prerequisites to create a secret and an IAM role to use with federated query:
+ An RDS PostgreSQL or Aurora PostgreSQL instance with user name and password authentication\.
+ An Amazon Redshift cluster with a cluster maintenance version that supports federated query\.
+ Connectivity between RDS PostgreSQL or Aurora PostgreSQL and Amazon Redshift\. Your RDS PostgreSQL or Aurora PostgreSQL instance must be able to accept connections from the Amazon Redshift cluster \(both leader node and compute nodes\)\. This ability is controlled by the security group attached to the RDS PostgreSQL or Aurora PostgreSQL instance\.

**To create a secret \(user name and password\) with Secrets Manager**

1. Sign in to the Secrets Manager console with the account that owns your RDS PostgreSQL or Aurora PostgreSQL instance\.

1. Choose **Store a new secret**\. 

1. Choose the **Credentials for RDS database** tile\. For **User name** and **Password**, enter values for your instance\. Confirm or choose a value for **Encryption key**\. Then choose the RDS database that your secret will access\. 

1. Enter a name for the secret, continue with the creation steps with the default choices, and choose **Store**\. 

1. View your secret and note the **Secret ARN** value that you created to identify the secret\. 

**To create a security policy using the secret**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Create a policy with JSON similar to the following\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "AccessSecret",
               "Effect": "Allow",
               "Action": [
                   "secretsmanager:GetResourcePolicy",
                   "secretsmanager:GetSecretValue",
                   "secretsmanager:DescribeSecret",
                   "secretsmanager:ListSecretVersionIds"
               ],
               "Resource": "arn:aws:secretsmanager:us-west-2:123456789012:secret:my-rds-secret-VNenFy"
           },
           {
               "Sid": "VisualEditor1",
               "Effect": "Allow",
               "Action": [
                   "secretsmanager:GetRandomPassword",
                   "secretsmanager:ListSecrets"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   You need list and read actions to retrieve the secret\. We recommend that you restrict the resource to the specific secret that you created by using the ARN of the secret to limit the resource\. You can also specify the permissions and resources using the visual editor on the IAM console\.

1. Give the policy a name and finish creating it\. 

1. Navigate to **IAM roles**\. 

1. Create an IAM role for **Redshift \- Customizable**\. 

1. Either attach the IAM policy you just created to an existing IAM role, or create a new IAM role and attach the policy\. 

1. Confirm that the IAM role **Trust relationships** contains `redshift.amazonaws.com`\. 

1. Note the **Role ARN** you created\. This ARN has access to the secret\. 

**To attach the IAM role to your Amazon Redshift cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **CLUSTERS**\. The clusters for your account in the current AWS Region are listed\. 

1. Choose the cluster name in the list to view more details about a cluster\.

1. For **Actions**, choose **Manage IAM roles**\. The **Manage IAM roles** page appears\.

1. Add your IAM role to the cluster\.