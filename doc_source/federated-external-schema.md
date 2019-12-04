# CREATE EXTERNAL SCHEMA<a name="federated-external-schema"></a>


|  | 
| --- |
| This is prerelease documentation for the federated query feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Creates an external schema in the current database\. You use this external schema to connect to RDS PostgreSQL and Aurora PostgreSQL databases\.  

All external tables must be created in an external schema\. You can't create local tables in external schemas\. 

## Syntax<a name="r_CREATE_EXTERNAL_SCHEMA-synopsis"></a>

```
CREATE EXTERNAL SCHEMA [IF NOT EXISTS] local_schema_name
FROM POSTGRES
DATABASE 'database_name' [SCHEMA 'schema_name']
URI 'hostname' [ PORT port_number ] 
[ OPTIONS 'options_list' ]            
IAM_ROLE 'iam-role-arn-string' 
SECRET_ARN 'ssm-secret-arn'
```

## Parameters<a name="federated_CREATE_EXTERNAL_SCHEMA-parameters"></a>

IF NOT EXISTS  
A clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists, rather than terminating with an error\. This clause is useful when scripting, so the script doesn’t fail if CREATE EXTERNAL SCHEMA tries to create a schema that already exists\. 

 *local\_schema\_name*   
The name of the external schema when you reference it from Amazon Redshift\. For more information about valid names, see [Names and Identifiers](r_names.md)\.

FROM POSTGRES  
A keyword that indicates where the external database is located\.   
POSTGRES indicates that the external database is defined in RDS PostgreSQL or Aurora PostgreSQL\. 

DATABASE '*database\_name*' \[SCHEMA '*schema\_name*'\]  
A keyword that indicates the name of the external database in RDS PostgreSQL or Aurora PostgreSQL\.   
The *schema\_name* indicates the schema in RDS PostgreSQL or Aurora PostgreSQL\. The default *schema\_name* is `public`\.

URI '*hostname*' \[ PORT *port\_number* \]  
The *hostname* URI and *port\_number* of an RDS PostgreSQL or Aurora PostgreSQL\. The *hostname* is the headnode of the replica set\. The endpoint must be reachable \(routable\) from the Amazon Redshift cluster that runs on your VPC by using a public IP address\. The default *port\_number* is 5432\.   
The RDS PostgreSQL or Aurora PostgreSQL must be in the same VPC as your Amazon Redshift cluster\. Create a security group linking Amazon Redshift and RDS PostgreSQL or Aurora PostgreSQL\. 

\[ OPTIONS '*options\_list*' \]  
The *options\_list* is a list of supported driver options specified as key\-value pairs separated by an ampersand \(`&`\)\. Supported options are `application_name` and `fallback_application_name`\.

IAM\_ROLE '*iam\-role\-arn\-string*'  
The Amazon Resource Name \(ARN\) for an IAM role that your cluster uses for authentication and authorization\.   

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```
You can chain roles so that your cluster can assume another IAM role, possibly belonging to another account\. You can chain up to 10 roles\. Attach to this IAM role an IAM permissions policy similar to the following\.  

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
For the steps to create an IAM role to use with federated query, see [Create a Secret and an IAM Role for Federated Query](federated-create-secret-iam-role.md)\. 

SECRET\_ARN '*ssm\-secret\-arn*'  
The *ssm\-secret\-arn* is the ARN of an RDS PostgreSQL or Aurora PostgreSQL secret created using AWS Secrets Manager\. For information about how to create and retrieve a `SecretARN` value, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) and [Retrieving the Secret Value Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_retrieve-secret.html) in the *AWS Secrets Manager User Guide*\.   
To use AWS Secrets Manager to manage access to *hostname* by Amazon Redshift, follow this general flow:   
+ Create an IAM role in the RDS PostgreSQL or Aurora PostgreSQL database instance and give it permission to access the appropriate schemas and tables\.
+ Publish the credentials of this IAM role to Secrets Manager\. 
+ In Amazon Redshift, create an external schema that references the external RDS PostgreSQL or Aurora PostgreSQL database \(this command\)\.
+ In Amazon Redshift, you can manage access to this external schema with *iam\-role\-arn\-string*\. 

## Usage Notes<a name="federated_CREATE_EXTERNAL_SCHEMA_usage"></a>

Use this command to register an external schema with Amazon Redshift\. To unregister the schema, use the [DROP SCHEMA](r_DROP_SCHEMA.md) command\. 

To view details for external schemas, query the following system views: 
+ [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md) 
+ [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) 
+ [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) 

## Examples<a name="federated_CREATE_EXTERNAL_SCHEMA_examples"></a>

The following example creates an external schema that references an Aurora PostgreSQL database\. 

```
CREATE EXTERNAL SCHEMA [IF NOT EXISTS] myRedshiftSchema

FROM POSTGRES
DATABASE 'aurora database name' SCHEMA 'aurora_schema'
URI 'endpoint to aurora hostname' PORT 5432 
OPTIONS 'application_name=tableau'           
IAM_ROLE 'arn:aws:iam::123456789012:role/MyAuroraRole' 
SECRET_ARN 'arn:aws:secretsmanager:us-east-2:123456789012:secret:development/MyTestDatabase-AbCdEf'
```