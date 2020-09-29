# Permissions to access other AWS Resources<a name="copy-usage_notes-access-permissions"></a>

 To move data between your cluster and another AWS resource, such as Amazon S3, Amazon DynamoDB, Amazon EMR, or Amazon EC2, your cluster must have permission to access the resource and perform the necessary actions\. For example, to load data from Amazon S3, COPY must have LIST access to the bucket and GET access for the bucket objects\. For information about minimum permissions, see [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](#copy-usage_notes-iam-permissions)\.

To get authorization to access the resource, your cluster must be authenticated\. You can choose either of the following authentication methods: 
+ [Role\-based access control](#copy-usage_notes-access-role-based) – For role\-based access control, you specify an AWS Identity and Access Management \(IAM\) role that your cluster uses for authentication and authorization\. To safeguard your AWS credentials and sensitive data, we strongly recommend using role\-based authentication\.
+ [Key\-based access control](#copy-usage_notes-access-key-based) – For key\-based access control, you provide the AWS access credentials \(access key ID and secret access key\) for an IAM user as plain text\. 

## Role\-based access control<a name="copy-usage_notes-access-role-based"></a>

With <a name="copy-usage_notes-access-role-based.phrase"></a>role\-based access control, your cluster temporarily assumes an IAM role on your behalf\. Then, based on the authorizations granted to the role, your cluster can access the required AWS resources\.

An IAM *role* is similar to an IAM user, in that it is an AWS identity with permissions policies that determine what the identity can and can't do in AWS\. However, instead of being uniquely associated with one user, a role can be assumed by any entity that needs it\. Also, a role doesn’t have any credentials \(a password or access keys\) associated with it\. Instead, if a role is associated with a cluster, access keys are created dynamically and provided to the cluster\.

We recommend using role\-based access control because it provides more secure, fine\-grained control of access to AWS resources and sensitive user data, in addition to safeguarding your AWS credentials\.

Role\-based authentication delivers the following benefits:
+ You can use AWS standard IAM tools to define an IAM role and associate the role with multiple clusters\. When you modify the access policy for a role, the changes are applied automatically to all clusters that use the role\.
+ You can define fine\-grained IAM policies that grant permissions for specific clusters and database users to access specific AWS resources and actions\.
+ Your cluster obtains temporary session credentials at run time and refreshes the credentials as needed until the operation completes\. If you use key\-based temporary credentials, the operation fails if the temporary credentials expire before it completes\.
+ Your access key ID and secret access key ID aren't stored or transmitted in your SQL code\.

To use role\-based access control, you must first create an IAM role using the Amazon Redshift service role type, and then attach the role to your cluster\. The role must have, at a minimum, the permissions listed in [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](#copy-usage_notes-iam-permissions)\. For steps to create an IAM role and attach it to your cluster, see [Authorizing Amazon Redshift to Access Other AWS Services On Your Behalf](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html) in the *Amazon Redshift Cluster Management Guide*\.

You can add a role to a cluster or view the roles associated with a cluster by using the Amazon Redshift Management Console, CLI, or API\. For more information, see [Associating an IAM Role With a Cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html) in the *Amazon Redshift Cluster Management Guide*\.

When you create an IAM role, IAM returns an Amazon Resource Name \(ARN\) for the role\. To specify an IAM role, provide the role ARN with either the [IAM_ROLE](copy-parameters-authorization.md#copy-iam-role) parameter or the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter\. 

For example, suppose the following role is attached to the cluster\.

```
"IamRoleArn": "arn:aws:iam::0123456789012:role/MyRedshiftRole"
```

The following COPY command example uses the IAM\_ROLE parameter with the ARN in the previous example for authentication and access to Amazon S3\.

```
copy customer from 's3://mybucket/mydata'  
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The following COPY command example uses the CREDENTIALS parameter to specify the IAM role\.

```
copy customer from 's3://mybucket/mydata' 
credentials 
'aws_iam_role=arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

## Key\-based access control<a name="copy-usage_notes-access-key-based"></a>

With <a name="copy-usage_notes-access-key-based.phrase"></a>key\-based access control, you provide the access key ID and secret access key for an IAM user that is authorized to access the AWS resources that contain the data\. You can user either the [ACCESS_KEY_ID and SECRET_ACCESS_KEY](copy-parameters-authorization.md#copy-access-key-id) parameters together or the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter\.

To authenticate using ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY, replace *<access\-key\-id>* and *<secret\-access\-key>* with an authorized user's access key ID and full secret access key as shown following\. 

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>';
```

To authenticate using the CREDENTIALS parameter, replace *<access\-key\-id>* and *<secret\-access\-key>* with an authorized user's access key ID and full secret access key as shown following\.

```
CREDENTIALS
'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>';
```

**Note**  
We strongly recommend using an IAM role for authentication instead of supplying a plain\-text access key ID and secret access key\. If you choose key\-based access control, never use your AWS account \(root\) credentials\. Always create an IAM user and provide that user's access key ID and secret access key\. For steps to create an IAM user, see [Creating an IAM User in Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)\.

The IAM user must have, at a minimum, the permissions listed in [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](#copy-usage_notes-iam-permissions)\.

### Temporary security credentials<a name="r_copy-temporary-security-credentials"></a>

 If you are using key\-based access control, you can further limit the access users have to your data by using temporary security credentials\. Role\-based authentication automatically uses temporary credentials\. 

**Note**  
We strongly recommend using [role-based access control](#copy-usage_notes-access-role-based.phrase) instead of creating temporary credentials and providing access key ID and secret access key as plain text\. Role\-based access controlautomatically uses temporary credentials\. 

Temporary security credentials provide enhanced security because they have short lifespans and can't be reused after they expire\. The access key ID and secret access key generated with the token can't be used without the token, and a user who has these temporary security credentials can access your resources only until the credentials expire\.

To grant users temporary access to your resources, you call AWS Security Token Service \(AWS STS\) API operations\. The AWS STS API operations return temporary security credentials consisting of a security token, an access key ID, and a secret access key\. You issue the temporary security credentials to the users who need temporary access to your resources\. These users can be existing IAM users, or they can be non\-AWS users\. For more information about creating temporary security credentials, see [Using Temporary Security Credentials](https://docs.aws.amazon.com/STS/latest/UsingSTS/Welcome.html) in the IAM User Guide\.

You can use either the [ACCESS_KEY_ID and SECRET_ACCESS_KEY](copy-parameters-authorization.md#copy-access-key-id) parameters together with the [SESSION_TOKEN](copy-parameters-authorization.md#copy-token) parameter or the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter\. You must also supply the access key ID and secret access key that were provided with the token\.

To authenticate using ACCESS\_KEY\_ID, SECRET\_ACCESS\_KEY, and SESSION\_TOKEN, replace *<temporary\-access\-key\-id>*, *<temporary\-secret\-access\-key>*, and *<temporary\-token>* as shown following\. 

```
ACCESS_KEY_ID '<temporary-access-key-id>'
SECRET_ACCESS_KEY '<temporary-secret-access-key>'
SESSION_TOKEN '<temporary-token>';
```

To authenticate using CREDENTIALS, include `token=<temporary-token>` in the credentials string as shown following\. 

```
CREDENTIALS
'aws_access_key_id=<temporary-access-key-id>;aws_secret_access_key=<temporary-secret-access-key>;token=<temporary-token>';
```

The following example shows a COPY command with temporary security credentials\.

```
copy table-name
from 's3://objectpath'
access_key_id '<temporary-access-key-id>'
secret_access_key '<temporary-secret-access-key>
token '<temporary-token>';
```

The following example loads the LISTING table with temporary credentials and file encryption\.

```
copy listing
from 's3://mybucket/data/listings_pipe.txt'
access_key_id '<temporary-access-key-id>'
secret_access_key '<temporary-secret-access-key>
token '<temporary-token>'
master_symmetric_key '<master-key>'
encrypted;
```

The following example loads the LISTING table using the CREDENTIALS parameter with temporary credentials and file encryption\.

```
copy listing
from 's3://mybucket/data/listings_pipe.txt'
credentials 
'aws_access_key_id=<temporary-access-key-id>;<aws_secret_access_key=<temporary-secret-access-key>;token=<temporary-token>;master_symmetric_key=<master-key>'
encrypted;
```

**Important**  
The temporary security credentials must be valid for the entire duration of the COPY or UNLOAD operation\. If the temporary security credentials expire during the operation, the command fails and the transaction is rolled back\. For example, if temporary security credentials expire after 15 minutes and the COPY operation requires one hour, the COPY operation fails before it completes\. If you use role\-based access, the temporary security credentials are automatically refreshed until the operation completes\.

## IAM permissions for COPY, UNLOAD, and CREATE LIBRARY<a name="copy-usage_notes-iam-permissions"></a>

The IAM role or IAM user referenced by the CREDENTIALS parameter must have, at a minimum, the following permissions:
+ For COPY from Amazon S3, permission to LIST the Amazon S3 bucket and GET the Amazon S3 objects that are being loaded, and the manifest file, if one is used\.
+ For COPY from Amazon S3, Amazon EMR, and remote hosts \(SSH\) with JSON\-formatted data, permission to LIST and GET the JSONPaths file on Amazon S3, if one is used\. 
+ For COPY from DynamoDB, permission to SCAN and DESCRIBE the DynamoDB table that is being loaded\. 
+ For COPY from an Amazon EMR cluster, permission for the `ListInstances` action on the Amazon EMR cluster\. 
+ For UNLOAD to Amazon S3, GET, LIST, and PUT permissions for the Amazon S3 bucket to which the data files are being unloaded\.
+ For CREATE LIBRARY from Amazon S3, permission to LIST the Amazon S3 bucket and GET the Amazon S3 objects being imported\.

**Note**  
If you receive the error message `S3ServiceException: Access Denied`, when running a COPY, UNLOAD, or CREATE LIBRARY command, your cluster doesn’t have proper access permissions for Amazon S3\.

You can manage IAM permissions by attaching an IAM policy to an IAM role that is attached to your cluster, to your IAM user, or to the group to which your IAM user belongs\. For example, the `AmazonS3ReadOnlyAccess` managed policy grants LIST and GET permissions to Amazon S3 resources\. For more information about IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html) in the *IAM User Guide*\. 