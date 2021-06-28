# Administrative setup<a name="admin-setup"></a>

Before you work with Amazon Redshift ML, complete the cluster setup and configure permissions for using Amazon Redshift ML\.

## Cluster setup for using Amazon Redshift ML<a name="cluster-setup"></a>

Before you work with Amazon Redshift ML, complete the following prerequisites\.

As an Amazon Redshift administrator, do the following one\-time setup:

1. Create an Amazon Redshift cluster using the AWS console or the AWS Command Line Interface \(AWS CLI\)\. You must attach the AWS Identity and Access Management \(IAM\) policy while creating the cluster\.

1. There are two ways you can create an IAM role required for using Amazon Redshift ML with Amazon SageMaker\.
   + A simple method is to create an IAM role with `AmazonS3FullAccess` and `AmazonSageMakerFullAccess` for use with Amazon Redshift ML\.
   + If you want to create an IAM role with a more restrictive policy, you can use the policy following\. You can also modify this policy to meet your needs\.

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Action": [
                     "cloudwatch:PutMetricData",
                     "ecr:BatchCheckLayerAvailability",
                     "ecr:BatchGetImage",
                     "ecr:GetAuthorizationToken",
                     "ecr:GetDownloadUrlForLayer",
                     "logs:CreateLogGroup",
                     "logs:CreateLogStream",
                     "logs:DescribeLogStreams",
                     "logs:PutLogEvents",
                     "sagemaker:*Job*"
                 ],
                 "Resource": "*"
             },
             {
                 "Effect": "Allow",
                 "Action": [
                     "iam:PassRole",
                     "s3:AbortMultipartUpload",
                     "s3:GetObject",
                     "s3:DeleteObject",
                     "s3:PutObject"
                 ],
                 "Resource": [
                     "arn:aws:iam::<your-account-id>:role/<your-role>",
                     "arn:aws:s3:::<your-s3-bucket>/*",
                     "arn:aws:s3:::redshift-downloads/*"
                 ]
             },
             {
                 "Effect": "Allow",
                 "Action": [
                     "s3:GetBucketLocation",
                     "s3:ListBucket"
                 ],
                 "Resource": [
                     "arn:aws:s3:::<your-s3-bucket>",
                     "arn:aws:s3:::redshift-downloads"
                 ]
             }
             // Optional section needed if you use AWS KMS keys.
             ,{
                 "Effect": "Allow",
                 "Action": [
                     "kms:CreateGrant",
                     "kms:Decrypt",
                     "kms:DescribeKey",
                     "kms:Encrypt",
                     "kms:GenerateDataKey*"
                  ],
                  "Resource": [
                     "arn:aws:kms:<your-region>:<your-account-id>:key/<your-kms-key>"
                  ]
             }
         ]
     }
     ```

1. To allow Amazon Redshift and SageMaker to assume the role to interact with other services, add the following trust policy to the IAM role\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "redshift.amazonaws.com",
             "sagemaker.amazonaws.com"
           ]
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

   The Amazon S3 bucket `redshift-downloads/redshift-ml/` is the location where the sample data used for other steps and examples is stored\. You can remove it if you don't need to load data from Amazon S3\. Or, replace it with other Amazon S3 buckets that you use to load data into Amazon Redshift\. 

   The **your\-account\-id**, **your\-role**, and **your\-s3\-bucket** are the ones you specify as part of your CREATE MODEL command\.

   \(Optional\) Use the AWS KMS keys section of the sample policy if you specify an AWS KMS key while using Amazon Redshift ML\. The **your\-kms\-key** value is the key that you use as part of your CREATE MODEL command\.

1. \(Optional\) Create an Amazon S3 bucket and an AWS KMS key that Amazon Redshift uses to store the training data sent to Amazon SageMaker and receive the trained model from Amazon SageMaker\.

1. \(Optional\) Create different combinations of IAM roles and Amazon S3 buckets for controlling access to different user groups\.

1. \(Optional\) When you enabled virtual private cloud \(VPC\) routing on your Amazon Redshift cluster, create an Amazon VPC endpoint and an SageMaker endpoint for the VPC that your Amazon Redshift cluster is in\. This enables that traffic can run through your VPC between services during CREATE MODEL\. For more information about VPC routing, see [Enhanced VPC routing in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html)\.

   When you specify a private VPC for your hyperparameter tuning job, add the following permissions:

   ```
   {
               "Effect": "Allow",
               "Action": [
               "ec2:CreateNetworkInterface",
               "ec2:CreateNetworkInterfacePermission",
               "ec2:DeleteNetworkInterface",
               "ec2:DeleteNetworkInterfacePermission",
               "ec2:DescribeNetworkInterfaces",
               "ec2:DescribeVpcs",
               "ec2:DescribeDhcpOptions",
               "ec2:DescribeSubnets",
               "ec2:DescribeSecurityGroups"
               ]
   }
   ```

For information on how to use the CREATE MODEL statement to start creating models for different use cases, see [CREATE MODEL](r_CREATE_MODEL.md)\.

## Managing permissions and ownership<a name="permissions-ownership"></a>

Similar to how Amazon Redshift manages other database objects, such as tables, views, or functions, Amazon Redshift binds model creation and use to access control mechanisms\. There are separate privileges for creating a model running prediction functions\. 

The following examples use two user groups, retention\_analyst\_grp \(model creator\) and marketing\_analyst\_grp \(model user\) to illustrate how Amazon Redshift manages access control\. The retention analyst creates machine learning models that other set of users can use through acquired privileges\. 

A superuser can GRANT USER or GROUP privilege to create machine learning models using the following statement\.

```
GRANT CREATE MODEL TO GROUP retention_analyst_grp;
```

Users or groups with this privilege can subsequently create a model in any schema in the cluster if a user has the usual CREATE privilege on the SCHEMA\. The machine learning model is part of the schema hierarchy in a similar way to tables, views, procedures, and user\-defined functions\. 

Assuming a schema demo\_ml already exists, grant the two user groups the permission on the schema as follows\.

```
GRANT CREATE, USAGE ON SCHEMA demo_ml TO GROUP retention_analyst_grp;
```

```
GRANT USAGE ON SCHEMA demo_ml TO GROUP marketing_analyst_grp;
```

You can enable other users to use the machine learning inference function by granting the EXECUTE privilege\. The following example uses the EXECUTE privilege to grant the marketing\_analyst\_grp GROUP the privilege to use the model\.

```
GRANT EXECUTE ON MODEL demo_ml.customer_churn_auto_model TO marketing_analyst_grp;
```

Use the REVOKE statement with CREATE MODEL and EXECUTE to revoke those privileges from users or groups\. For more information on permission control commands, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\.