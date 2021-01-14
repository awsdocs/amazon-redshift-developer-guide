# Cluster setup for using Amazon Redshift ML<a name="cluster-setup"></a>

Before you work with Amazon Redshift ML, complete the following prerequisites\.

As an Amazon Redshift administrator, do the following one\-time setup:

1. Create an Amazon Redshift cluster with the SQL\_PREVIEW track using the AWS console\. You can also use the AWS Command Line Interface \(AWS CLI\) to create the cluster in the preview track\. You must attach the AWS Identity and Access Management \(IAM\) policy while creating the cluster\.

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
             // Optional section needed if you use KMS keys.
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

   \(Optional\) Use the KMS keys section of the sample policy if you specify a KMS key while using Amazon Redshift ML\. The **your\-kms\-key** is the key that you use as part of your CREATE MODEL command22\.

1. \(Optional\) Create an Amazon S3 bucket and a AWS KMS key that Amazon Redshift uses to store the training data sent to Amazon SageMaker and receive the trained model from Amazon SageMaker\.

1. \(Optional\) Create different combinations of IAM roles and Amazon S3 buckets for controlling access to different user groups\.

For information on how to use the CREATE MODEL statement to start creating models for different use cases, see [CREATE MODEL](r_CREATE_MODEL.md)\.