# Credentials and access permissions<a name="loading-data-access-permissions"></a>

 To load or unload data using another AWS resource, such as Amazon S3, Amazon DynamoDB, Amazon EMR, or Amazon EC2, your cluster must have permission to access the resource and perform the necessary actions to access the data\. For example, to load data from Amazon S3, COPY must have LIST access to the bucket and GET access for the bucket objects\. 

To obtain authorization to access a resource, your cluster must be authenticated\. You can choose either role\-based access control or key\-based access control\. This section presents an overview of the two methods\. For complete details and examples, see [Permissions to access other AWS Resources](copy-usage_notes-access-permissions.md)\.

## Role\-based access control<a name="loading-data-access-role-based"></a>

With role\-based access control, your cluster temporarily assumes an AWS Identity and Access Management \(IAM\) role on your behalf\. Then, based on the authorizations granted to the role, your cluster can access the required AWS resources\.

We recommend using role\-based access control because it is provides more secure, fine\-grained control of access to AWS resources and sensitive user data, in addition to safeguarding your AWS credentials\.

To use role\-based access control, you must first create an IAM role using the Amazon Redshift service role type, and then attach the role to your cluster\. The role must have, at a minimum, the permissions listed in [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\. For steps to create an IAM role and attach it to your cluster, see [Creating an IAM Role to Allow Your Amazon Redshift Cluster to Access AWS Services](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html#authorizing-redshift-service-creating-an-iam-role) in the *Amazon Redshift Cluster Management Guide*\.

You can add a role to a cluster or view the roles associated with a cluster by using the Amazon Redshift Management Console, CLI, or API\. For more information, see [Authorizing COPY and UNLOAD Operations Using IAM Roles](https://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html) in the *Amazon Redshift Cluster Management Guide*\.

When you create an IAM role, IAM returns an Amazon Resource Name \(ARN\) for the role\. To execute a COPY command using an IAM role, provide the role ARN using the IAM\_ROLE parameter or the CREDENTIALS parameter\.

The following COPY command example uses IAM\_ROLE parameter with the role `MyRedshiftRole` for authentication\.

```
copy customer from 's3://mybucket/mydata' 
iam_role 'arn:aws:iam::12345678901:role/MyRedshiftRole';
```

## Key\-based access control<a name="loading-data-access-key-based"></a>

With key\-based access control, you provide the access key ID and secret access key for an IAM user that is authorized to access the AWS resources that contain the data\. 

**Note**  
We strongly recommend using an IAM role for authentication instead of supplying a plain\-text access key ID and secret access key\. If you choose key\-based access control, never use your AWS account \(root\) credentials\. Always create an IAM user and provide that user's access key ID and secret access key\. For steps to create an IAM user, see [Creating an IAM User in Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)\.

To authenticate using IAM user credentials, replace *<access\-key\-id>* and *<secret\-access\-key* with an authorized user's access key ID and full secret access key for the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters as shown following\.

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>';
```

The AWS IAM user must have, at a minimum, the permissions listed in [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\.