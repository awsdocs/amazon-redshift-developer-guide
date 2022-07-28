# Cluster and configure setup for Amazon Redshift ML administration<a name="admin-setup"></a>

Before you work with Amazon Redshift ML, complete the cluster setup and configure permissions for using Amazon Redshift ML\.

## Cluster setup for using Amazon Redshift ML<a name="cluster-setup"></a>

Before you work with Amazon Redshift ML, complete the following prerequisites\.

As an Amazon Redshift administrator, do the following one\-time setup\.

**To perform one\-time cluster setup for Amazon Redshift ML**

1. Create a Redshift cluster using the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\. Make sure to attach the AWS Identity and Access Management \(IAM\) policy while creating the cluster\. For more information about permissions required to use Amazon Redshift ML with Amazon SageMaker, see [Permissions required to use Amazon Redshift machine learning \(ML\) with Amazon SageMaker](https://docs.aws.amazon.com/redshift/latest/mgmt/redshift-iam-access-control-identity-based.html#iam-permission-ml)\.

1. Create the IAM role required for using Amazon Redshift ML with Amazon SageMaker in one of the following ways:
   + A simple operation is to create an IAM role with `AmazonS3FullAccess` and `AmazonSageMakerFullAccess` policies for use with Amazon Redshift ML\.
   + We recommend that you create an IAM role through the Amazon Redshift console that has the `AmazonRedshiftAllCommandsFullAccess` policy with permissions to run SQL commands, such as CREATE MODEL\. Amazon Redshift uses a seamless API\-based mechanism to programmatically create IAM roles in your AWS account on your behalf\. Amazon Redshift automatically attaches existing AWS managed policies to the IAM role\. This approach means that you can stay within the Amazon Redshift console and don't have to switch to the IAM console for role creation\. For more information, see [Creating an IAM role as default for Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/default-iam-role.html)\.

     When an IAM role is created as the default for your cluster, include `redshift` as part of the resource name or use a Redshift\-specific tag to tag those resources\.

     If your cluster has enhanced Amazon VPC routing turned on, you can use an IAM role created through the Amazon Redshift console\. This IAM role has the `AmazonRedshiftAllCommandsFullAccess` policy attached and adds the following permissions to the policy\. These additional permissions allow Amazon Redshift to create and delete an elastic network interface \(ENI\) in your account and attach it to compilation tasks running on Amazon EC2 or Amazon ECS\. Doing this enables objects in your Amazon S3 buckets to be accessed only from within a virtual private cloud \(VPC\) with internet access blocked\.

     ```
     {
         "Effect": "Allow",
         "Action": [
             "ec2:DescribeVpcEndpoints",
             "ec2:DescribeDhcpOptions",
             "ec2:DescribeVpcs",
             "ec2:DescribeSubnets",
             "ec2:DescribeSecurityGroups",
             "ec2:DescribeNetworkInterfaces",
             "ec2:DeleteNetworkInterfacePermission",
             "ec2:DeleteNetworkInterface",
             "ec2:CreateNetworkInterfacePermission",
             "ec2:CreateNetworkInterface",
             "ec2:ModifyNetworkInterfaceAttribute"
         ],
         "Resource": "*"
     }
     ```
   + If you want to create an IAM role with a more restrictive policy, you can use the policy following\. You can also modify this policy to meet your needs\.

     The Amazon S3 bucket `redshift-downloads/redshift-ml/` is the location where the sample data used for other steps and examples is stored\. You can remove it if you don't need to load data from Amazon S3\. Or, replace it with other Amazon S3 buckets that you use to load data into Amazon Redshift\. 

     The *`your-account-id`*, *`your-role`*, and *`your-s3-bucket`* values are the ones that you specify as part of your CREATE MODEL command\.

     \(Optional\) Use the AWS KMS keys section of the sample policy if you specify an AWS KMS key while using Amazon Redshift ML\. The *`your-kms-key`* value is the key that you use as part of your CREATE MODEL command\.

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

1. \(Optional\) Create an Amazon S3 bucket and an AWS KMS key\. These are for Amazon Redshift to use to store the training data sent to Amazon SageMaker and receive the trained model from Amazon SageMaker\.

1. \(Optional\) Create different combinations of IAM roles and Amazon S3 buckets for controlling access to different user groups\.

1. \(Optional\) When you turn on VPC routing for your Redshift cluster, create an Amazon S3 endpoint and a SageMaker endpoint for the VPC that your Redshift cluster is in\. Doing this makes it possible for traffic to run through your VPC between services during CREATE MODEL\. For more information about VPC routing, see [Enhanced VPC routing in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html)\.

   For more information about permissions required to specify a private VPC for your hyperparameter tuning job, see [Permissions required to use Amazon Redshift ML with Amazon SageMaker](https://docs.aws.amazon.com/redshift/latest/mgmt/redshift-iam-access-control-identity-based.html)\.

For information on how to use the CREATE MODEL statement to start creating models for different use cases, see [CREATE MODEL](r_CREATE_MODEL.md)\.

## Managing permissions and ownership<a name="permissions-ownership"></a>

Just as with other database objects, such as tables or functions, Amazon Redshift binds creating and using ML models to access control mechanisms\. There are separate permissions for creating a model that runs prediction functions\. 

The following examples use two user groups, `retention_analyst_grp` \(model creator\) and `marketing_analyst_grp` \(model user\) to illustrate how Amazon Redshift manages access control\. The retention analyst creates machine learning models that the other set of users can use through acquired permissions\. 

A superuser can GRANT USER or GROUP permission to create machine learning models using the following statement\.

```
GRANT CREATE MODEL TO GROUP retention_analyst_grp;
```

Users or groups with this permission can create a model in any schema in the cluster if a user has the usual CREATE permission on the SCHEMA\. The machine learning model is part of the schema hierarchy in a similar way to tables, views, procedures, and user\-defined functions\. 

Assuming a schema `demo_ml` already exists, grant the two user groups the permission on the schema as follows\.

```
GRANT CREATE, USAGE ON SCHEMA demo_ml TO GROUP retention_analyst_grp;
```

```
GRANT USAGE ON SCHEMA demo_ml TO GROUP marketing_analyst_grp;
```

To let other users use your machine learning inference function, grant the EXECUTE permission\. The following example uses the EXECUTE permission to grant the marketing\_analyst\_grp GROUP the permission to use the model\.

```
GRANT EXECUTE ON MODEL demo_ml.customer_churn_auto_model TO GROUP marketing_analyst_grp;
```

Use the REVOKE statement with CREATE MODEL and EXECUTE to revoke those permissions from users or groups\. For more information on permission control commands, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\.