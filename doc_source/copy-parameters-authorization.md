# Authorization parameters<a name="copy-parameters-authorization"></a>

The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an [AWS Identity and Access Management \(IAM\) role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) that is attached to your cluster \(*role\-based access control*\) or by providing the access credentials for an IAM user \(*key\-based access control*\)\. For increased security and flexibility, we recommend using IAM role\-based access control\. COPY can also use temporary credentials to limit access to your load data, and you can encrypt your load data on Amazon S3\. 

The following topics provide more details and examples of authentication options:
+ [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)
+ [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based)
+ [Key\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based)

Use one of the following to provide authorization for the COPY command: 
+ [IAM_ROLE](#copy-iam-role) parameter
+ [ACCESS_KEY_ID and SECRET_ACCESS_KEY](#copy-access-key-id) parameters
+ [CREDENTIALS](#copy-credentials) clause<a name="copy-authorization-parameters-list"></a>

IAM\_ROLE '*iam\-role\-arn*'  <a name="copy-iam-role"></a>
The Amazon Resource Name \(ARN\) for an IAM role that your cluster uses for authentication and authorization\. If you specify IAM\_ROLE, you can't use ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY, SESSION\_TOKEN, or CREDENTIALS\.  
The following shows the syntax for the IAM\_ROLE parameter\.   

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```
For more information, see [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based)\. 

ACCESS\_KEY\_ID '*access\-key\-id *' SECRET\_ACCESS\_KEY '*secret\-access\-key*'  <a name="copy-access-key-id"></a>
The access key ID and secret access key for an IAM user that is authorized to access the AWS resources that contain the data\. ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY must be used together\. Optionally, you can provide temporary access credentials and also specify the [SESSION_TOKEN](#copy-token) parameter\.   
The following shows the syntax for the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters\.   

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>';
```
For more information, see [Key\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based)\.   
If you specify ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY, you can't use IAM\_ROLE or CREDENTIALS\.   
Instead of providing access credentials as plain text, we strongly recommend using role\-based authentication by specifying the IAM\_ROLE parameter\. For more information, see [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based)\. 

SESSION\_TOKEN '*temporary\-token*'  <a name="copy-token"></a>
The session token for use with temporary access credentials\. When SESSION\_TOKEN is specified, you must also use ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY to provide temporary access key credentials\. If you specify SESSION\_TOKEN you can't use IAM\_ROLE or CREDENTIALS\. For more information, see [Temporary security credentials](copy-usage_notes-access-permissions.md#r_copy-temporary-security-credentials) in the IAM User Guide\.  
Instead of creating temporary security credentials, we strongly recommend using role\-based authentication\. When you authorize using an IAM role, Amazon Redshift automatically creates temporary user credentials for each session\. For more information, see [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based)\. 
The following shows the syntax for the SESSION\_TOKEN parameter with the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters\.   

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>'
SESSION_TOKEN '<temporary-token>';
```
If you specify SESSION\_TOKEN you can't use CREDENTIALS or IAM\_ROLE\. 

\[WITH\] CREDENTIALS \[AS\] '*credentials\-args*'  <a name="copy-credentials"></a>
A clause that indicates the method your cluster will use when accessing other AWS resources that contain data files or manifest files\. You can't use the CREDENTIALS parameter with IAM\_ROLE or ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY\.  
For increased flexibility, we recommend using the [IAM_ROLE](#copy-iam-role) or [ACCESS_KEY_ID and SECRET_ACCESS_KEY](#copy-access-key-id) parameters instead of the CREDENTIALS parameter\.
Optionally, if the [ENCRYPTED](copy-parameters-data-source-s3.md#copy-encrypted) parameter is used, the *credentials\-args* string also provides the encryption key\.  
The *credentials\-args* string is case\-sensitive and must not contain spaces\.  
The keywords WITH and AS are optional and are ignored\.  
You can specify either [role-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based.phrase) or [key-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based.phrase)\. In either case, the IAM role or IAM user must have the permissions required to access the specified AWS resources\. For more information, see [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\.   
To safeguard your AWS credentials and protect sensitive data, we strongly recommend using role\-based access control\. 
To specify role\-based access control, provide the *credentials\-args* string in the following format\.  

```
'aws_iam_role=arn:aws:iam::<aws-account-id>:role/<role-name>'
```
To specify key\-based access control, provide the *credentials\-args* in the following format\.  

```
'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>'
```
To use temporary token credentials, you must provide the temporary access key ID, the temporary secret access key, and the temporary token\. The *credentials\-args* string is in the following format\.   

```
CREDENTIALS
'aws_access_key_id=<temporary-access-key-id>;aws_secret_access_key=<temporary-secret-access-key>;token=<temporary-token>'
```
 For more information, see [Temporary security credentials](copy-usage_notes-access-permissions.md#r_copy-temporary-security-credentials)\.  
If the [ENCRYPTED](copy-parameters-data-source-s3.md#copy-encrypted) parameter is used, the *credentials\-args* string is in the following format, where *<master\-key>* is the value of the master key that was used to encrypt the files\.  

```
CREDENTIALS
'<credentials-args>;master_symmetric_key=<master-key>'
```
For example, the following COPY command uses role\-based access control with an encryption key\.  

```
copy customer from 's3://mybucket/mydata' 
credentials 
'aws_iam_role=arn:aws:iam::<account-id>:role/<role-name>;master_symmetric_key=<master-key>'
```
The following COPY command shows key\-based access control with an encryption key\.  

```
copy customer from 's3://mybucket/mydata' 
credentials 
'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>;master_symmetric_key=<master-key>'
```