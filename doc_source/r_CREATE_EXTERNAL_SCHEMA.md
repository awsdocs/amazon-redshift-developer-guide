# CREATE EXTERNAL SCHEMA<a name="r_CREATE_EXTERNAL_SCHEMA"></a>

Creates a new external schema in the current database\. You can use this external schema to connect to Amazon RDS for PostgreSQL or Amazon Aurora with PostgreSQL compatibility databases\. You can also create an external schema that references a database in an external data catalog such as AWS Glue, Athena, or a database in an Apache Hive metastore, such as Amazon EMR\.

The owner of this schema is the issuer of the CREATE EXTERNAL SCHEMA command\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. To grant access to the schema to other users or user groups, use the [GRANT](r_GRANT.md) command\. 

You can't use the GRANT or REVOKE commands for permissions on an external table\. Instead, grant or revoke the permissions on the external schema\. 

**Note**  
If you currently have Redshift Spectrum external tables in the Amazon Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your AWS Identity and Access Management \(IAM\) policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](https://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

To view details for external schemas, query the [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md) system view\. 

## Syntax<a name="r_CREATE_EXTERNAL_SCHEMA-synopsis"></a>

The following syntax describes the CREATE EXTERNAL SCHEMA command used to reference data using an external data catalog\. For more information, see [Querying external data using Amazon Redshift Spectrum](c-using-spectrum.md)\.

```
CREATE EXTERNAL SCHEMA [IF NOT EXISTS] local_schema_name
FROM { [ DATA CATALOG ] |  HIVE METASTORE |  POSTGRES }
DATABASE 'database_name'
[ REGION 'aws-region' ]
[ URI 'hive_metastore_uri' [ PORT port_number ] ]
IAM_ROLE 'iam-role-arn-string'
SECRET_ARN 'ssm-secret-arn'             
[ CATALOG_ROLE 'catalog-role-arn-string' ] 
[ CREATE EXTERNAL DATABASE IF NOT EXISTS ]
```

The following syntax describes the CREATE EXTERNAL SCHEMA command used to reference data using a federated query\. For more information, see [Querying data with federated queries in Amazon Redshift](federated-overview.md)\.

```
CREATE EXTERNAL SCHEMA [IF NOT EXISTS] local_schema_name
FROM  POSTGRES
DATABASE 'database_name' [SCHEMA 'schema_name']
URI 'hostname' [ PORT port_number ] 
IAM_ROLE 'iam-role-arn-string'
SECRET_ARN 'ssm-secret-arn'
```

## Parameters<a name="r_CREATE_EXTERNAL_SCHEMA-parameters"></a>

IF NOT EXISTS  
A clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists, rather than terminating with an error\. This clause is useful when scripting, so the script doesn’t fail if CREATE EXTERNAL SCHEMA tries to create a schema that already exists\. 

local\_schema\_name  
The name of the new external schema\. For more information about valid names, see [Names and identifiers](r_names.md)\.

FROM \[ DATA CATALOG \] \| HIVE METASTORE   
A keyword that indicates where the external database is located\.   
DATA CATALOG indicates that the external database is defined in the Athena data catalog or the AWS Glue Data Catalog\.   
If the external database is defined in an external Data Catalog in a different AWS Region, the REGION parameter is required\. DATA CATALOG is the default\.  
HIVE METASTORE indicates that the external database is defined in an Apache Hive metastore\. If HIVE METASTORE, is specified, URI is required\.   
POSTGRES indicates that the external database is defined in RDS PostgreSQL or Aurora PostgreSQL\.

DATABASE '*database\_name*' \[SCHEMA '*schema\_name*'\]  
A keyword that indicates the name of the external database in RDS PostgreSQL or Aurora PostgreSQL\.   
The *schema\_name* indicates the schema in RDS PostgreSQL or Aurora PostgreSQL\. The default *schema\_name* is `public`\.

REGION '*aws\-region*'  
If the external database is defined in an Athena data catalog or the AWS Glue Data Catalog, the AWS Region in which the database is located\. This parameter is required if the database is defined in an external Data Catalog\. 

URI '*hive\_metastore\_uri*' \[ PORT port\_number \]  
The hostname URI and port\_number of an RDS PostgreSQL or Aurora PostgreSQL\. The *hostname* is the head node of the replica set\. The endpoint must be reachable \(routable\) from the Amazon Redshift cluster\. The default port\_number is 5432\.   
If the database is in a Hive metastore, specify the URI and optionally the port number for the metastore\. The default port number is 9083\.   
A URI doesn't contain a protocol specification \("http://"\)\. An example valid URI: `uri '172.10.10.10'`\.   
The RDS PostgreSQL or Aurora PostgreSQL must be in the same VPC as your Amazon Redshift cluster\. Create a security group linking Amazon Redshift and RDS PostgreSQL or Aurora PostgreSQL\. 

IAM\_ROLE '*iam\-role\-arn\-string*'  
The Amazon Resource Name \(ARN\) for an IAM role that your cluster uses for authentication and authorization\. As a minimum, the IAM role must have permission to perform a LIST operation on the Amazon S3 bucket to be accessed and a GET operation on the Amazon S3 objects the bucket contains\. If the external database is defined in an Amazon Athena data catalog or the AWS Glue Data Catalog, the IAM role must have permission to access Athena unless CATALOG\_ROLE is specified\. For more information, see [IAM policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)\. The following shows the syntax for the IAM\_ROLE parameter string for a single ARN\.  

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```
You can chain roles so that your cluster can assume another IAM role, possibly belonging to another account\. You can chain up to 10 roles\. For more information, see [Chaining IAM roles in Amazon Redshift Spectrum](c-spectrum-iam-policies.md#c-spectrum-chaining-roles)\.   
 To this IAM role, attach an IAM permissions policy similar to the following\.  

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
For the steps to create an IAM role to use with federated query, see [Creating a secret and an IAM role to use federated queries](federated-create-secret-iam-role.md)\.   
Don't include spaces in the list of chained roles\.
The following shows the syntax for chaining three roles\.  

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-1-name>,arn:aws:iam::<aws-account-id>:role/<role-2-name>,arn:aws:iam::<aws-account-id>:role/<role-3-name>'
```

SECRET\_ARN '*ssm\-secret\-arn*'  
The Amazon Resource Name \(ARN\) of an RDS PostgreSQL or Aurora PostgreSQL secret created using AWS Secrets Manager\. For information about how to create and retrieve an ARN for a secret, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) and [Retrieving the Secret Value Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_retrieve-secret.html) in the *AWS Secrets Manager User Guide*\. 

CATALOG\_ROLE '*catalog\-role\-arn\-string*'  
The ARN for an IAM role that your cluster uses for authentication and authorization for the data catalog\. If CATALOG\_ROLE isn't specified, Amazon Redshift uses the specified IAM\_ROLE\. The catalog role must have permission to access the Data Catalog in AWS Glue or Athena\. For more information, see [IAM policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)\. The following shows the syntax for the CATALOG\_ROLE parameter string for a single ARN\.  

```
CATALOG_ROLE 'arn:aws:iam::<aws-account-id>:role/<catalog-role>'
```
You can chain roles so that your cluster can assume another IAM role, possibly belonging to another account\. You can chain up to 10 roles\. For more information, see [Chaining IAM roles in Amazon Redshift Spectrum](c-spectrum-iam-policies.md#c-spectrum-chaining-roles)\.   
The list of chained roles must not include spaces\.
The following shows the syntax for chaining three roles\.  

```
CATALOG_ROLE 'arn:aws:iam::<aws-account-id>:role/<catalog-role-1-name>,arn:aws:iam::<aws-account-id>:role/<catalog-role-2-name>,arn:aws:iam::<aws-account-id>:role/<catalog-role-3-name>'
```

CREATE EXTERNAL DATABASE IF NOT EXISTS  
A clause that creates an external database with the name specified by the DATABASE argument, if the specified external database doesn't exist\. If the specified external database exists, the command makes no changes\. In this case, the command returns a message that the external database exists, rather than terminating with an error\.  
You can't use CREATE EXTERNAL DATABASE IF NOT EXISTS with HIVE METASTORE\.  
To use CREATE EXTERNAL DATABASE IF NOT EXISTS with a Data Catalog enabled for AWS Lake Formation, you need `CREATE_DATABASE` permission on the Data Catalog\. 

## Usage notes<a name="r_CREATE_EXTERNAL_SCHEMA_usage"></a>

For limits when using the Athena data catalog, see [Athena Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#amazon-athena-limits) in the AWS General Reference\.

For limits when using the AWS Glue Data Catalog, see [AWS Glue Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_glue) in the AWS General Reference\.

These limits don’t apply to a Hive metastore\.

To unregister the schema, use the [DROP SCHEMA](r_DROP_SCHEMA.md) command\. 

To view details for external schemas, query the following system views: 
+ [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md) 
+ [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) 
+ [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) 

## Examples<a name="r_CREATE_EXTERNAL_SCHEMA_examples"></a>

The following example creates an external schema using a database in an Athena data catalog named `sampledb` in the US West \(Oregon\) Region\.

```
create external schema spectrum_schema
from data catalog
database 'sampledb'
region 'us-west-2' 
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole';
```

The following example creates an external schema and creates a new external database named `spectrum_db`\.

```
create external schema spectrum_schema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole'
create external database if not exists;
```

The following example creates an external schema using a Hive metastore database named `hive_db`\.

```
create external schema hive_schema
from hive metastore
database 'hive_db'
uri '172.10.10.10' port 99
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole';
```

The following example chains roles to use the role `myS3Role` for accessing Amazon S3 and uses `myAthenaRole` for data catalog access\. For more information, see [Chaining IAM roles in Amazon Redshift Spectrum](c-spectrum-iam-policies.md#c-spectrum-chaining-roles)\.

```
create external schema spectrum_schema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/myRedshiftRole,arn:aws:iam::123456789012:role/myS3Role'
catalog_role 'arn:aws:iam::123456789012:role/myAthenaRole'
create external database if not exists;
```

The following example creates an external schema that references an Aurora PostgreSQL database\. 

```
CREATE EXTERNAL SCHEMA [IF NOT EXISTS] myRedshiftSchema
FROM POSTGRES
DATABASE 'my_aurora_db' SCHEMA 'my_aurora_schema'
URI 'endpoint to aurora hostname' PORT 5432            
IAM_ROLE 'arn:aws:iam::123456789012:role/MyAuroraRole' 
SECRET_ARN 'arn:aws:secretsmanager:us-east-2:123456789012:secret:development/MyTestDatabase-AbCdEf'
```