# CREATE EXTERNAL SCHEMA<a name="r_CREATE_EXTERNAL_SCHEMA"></a>

Creates a new external schema in the current database\. The owner of this schema is the issuer of the CREATE EXTERNAL SCHEMA command\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md) to change the owner\. Use the [GRANT](r_GRANT.md) command to grant access to the schema to other users or groups\. 

You can't use the GRANT or REVOKE commands for permissions on an external table\. Instead, grant or revoke the permissions on the external schema\. 

An Amazon Redshift external schema references a database in an external data catalog in AWS Glue or in Amazon Athena or a database in an Apache Hive metastore, such as Amazon EMR\. 

**Note**  
If you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

All external tables must be created in an external schema\. You can't create local tables in external schemas\. For more information, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\. 

External schemas do not support search paths\.

To view details for external schemas, query the [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md) system view\. 

## Syntax<a name="r_CREATE_EXTERNAL_SCHEMA-synopsis"></a>

## Parameters<a name="r_CREATE_EXTERNAL_SCHEMA-parameters"></a>

IF NOT EXISTS  
A clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists, rather than terminating with an error\. This clause is useful when scripting, so the script doesn’t fail if CREATE EXTERNAL SCHEMA tries to create a schema that already exists\. 

 *schema\_name*   
The name of the new external schema\. For more information about valid names, see [Names and Identifiers](r_names.md)\.

FROM \[ DATA CATALOG \] | HIVE METASTORE   
A keyword that indicates where the external database is located\.   
DATA CATALOG indicates that the external database is defined in the Athena data catalog\.   
If the external database is defined in an Athena data catalog in a different region, the REGION parameter is required\. DATA CATALOG is the default\.  
HIVE METASTORE indicates that the external database is defined in a Hive metastore\. If HIVE METASTORE, is specified, URI is required\. 

REGION '*aws\-region*'  
If the external database is defined in an Athena data catalog, the region in which the database is located\. This parameter is required if the database is defined in an Athena data catalog\. 

URI '*hive\_metastore\_uri*' \[ PORT port\_number \]  
If the database is in a Hive metastore, specify the URI and optionally the port number for the metastore\. The default port number is 9083\. 

IAM\_ROLE '*iam\-role\-arn*'   
The Amazon Resource Name \(ARN\) for an IAM role that your cluster uses for authentication and authorization\. As a minimum, the IAM role must have permission to perform a LIST operation on the Amazon S3 bucket to be accessed and a GET operation on the S3 objects the bucket contains\. If the external database is defined in an Athena data catalog, the IAM role must have permission to access Athena\. For more information, see [IAM Policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)\. The following shows the syntax for the IAM\_ROLE parameter\.  

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```

 CREATE EXTERNAL DATABASE IF NOT EXISTS  
A clause that creates an external database with the name specified by the DATABASE argument, if the specified external database doesn't exist\. If the specified external database exists, the command makes no changes\. In this case, the command returns a message that the external database exists, rather than terminating with an error\.  
CREATE EXTERNAL DATABASE IF NOT EXISTS can't be used with HIVE METASTORE\.

## Usage Notes<a name="r_CREATE_EXTERNAL_SCHEMA_usage"></a>

When using the Athena data catalog, the following limits apply:

+ A maximum of 100 databases per account\.

+ A maximum of 100 tables per database\.

+ A maximum of 20,000 partitions per table\.

You can request a limit increase by contacting AWS Support\.

These limits don’t apply to a Hive metastore\.

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