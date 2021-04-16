# IAM policies for Amazon Redshift Spectrum<a name="c-spectrum-iam-policies"></a>

By default, Amazon Redshift Spectrum uses the AWS Glue Data Catalog in AWS Regions that support AWS Glue\. In other AWS Regions, Redshift Spectrum uses the Athena Data Catalog\. Your cluster needs authorization to access your external data catalog in AWS Glue or Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. If you use an Apache Hive metastore to manage your data catalog, you don't need to provide access to Athena\.

You can chain roles so that your cluster can assume other roles not attached to the cluster\. For more information, see [Chaining IAM roles in Amazon Redshift Spectrum](#c-spectrum-chaining-roles)\.

The AWS Glue catalog that you access might be encrypted to increase security\. If the AWS Glue catalog is encrypted, you need the AWS KMS key for AWS Glue to access the AWS Glue Data Catalog\. For more information, see [Encrypting Your AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *[AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)\.*

**Topics**
+ [Amazon S3 permissions](#spectrum-iam-policies-s3)
+ [Cross\-account Amazon S3 permissions](#spectrum-iam-policies-cross-account)
+ [Policies to grant or restrict access using Redshift Spectrum](#spectrum-iam-policies-spectrum-only)
+ [Policies to grant minimum permissions](#spectrum-iam-policies-minimum-permissions)
+ [Chaining IAM roles in Amazon Redshift Spectrum](#c-spectrum-chaining-roles)
+ [Controlling access to the AWS Glue Data Catalog](#c-spectrum-glue-acess)

**Note**  
If you currently have Redshift Spectrum external tables in the Athena Data Catalog, you can migrate your Athena Data Catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](https://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

## Amazon S3 permissions<a name="spectrum-iam-policies-s3"></a>

At a minimum, your cluster needs GET and LIST access to your Amazon S3 bucket\. If your bucket is not in the same AWS account as your cluster, your bucket must also authorize your cluster to access the data\. For more information, see [ Authorizing Amazon Redshift to Access Other AWS Services on Your Behalf](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html)\. 

**Note**  
The Amazon S3 bucket can't use a bucket policy that restricts access only from specific VPC endpoints\.

The following policy grants GET and LIST access to any Amazon S3 bucket\. The policy allows access to Amazon S3 buckets for Redshift Spectrum as well as COPY operations\.

```
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": ["s3:Get*", "s3:List*"],
		"Resource": "*"
	}]
}
```

The following policy grants GET and LIST access to your Amazon S3 bucket named `myBucket`\.

```
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": ["s3:Get*", "s3:List*"],
		"Resource": "arn:aws:s3:::myBucket/*"
	}]
}
```

## Cross\-account Amazon S3 permissions<a name="spectrum-iam-policies-cross-account"></a>

To grant Redshift Spectrum permission to access data in an Amazon S3 bucket that belongs to another AWS account, add the following policy to the Amazon S3 bucket\. For more information, see [Granting Cross\-Account Bucket Permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access-example2.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Example permissions",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::redshift-account:role/spectrumrole"
            },
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListMultipartUploadParts",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## Policies to grant or restrict access using Redshift Spectrum<a name="spectrum-iam-policies-spectrum-only"></a>

To grant access to an Amazon S3 bucket only using Redshift Spectrum, include a condition that allows access for the user agent `AWS Redshift/Spectrum`\. The following policy allows access to Amazon S3 buckets only for Redshift Spectrum\. It excludes other access, such as COPY operations\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": ["s3:Get*", "s3:List*"],
        "Resource": "arn:aws:s3:::myBucket/*",
                "Condition": {"StringEquals": {"aws:UserAgent": "AWS Redshift/Spectrum"}}
    }]
}
```

Similarly, you might want to create an IAM role that allows access for COPY operations, but excludes Redshift Spectrum access\. To do so, include a condition that denies access for the user agent "AWS Redshift/Spectrum"\. The following policy allows access to an Amazon S3 bucket with the exception of Redshift Spectrum\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": ["s3:Get*", "s3:List*"],
        "Resource": "arn:aws:s3:::myBucket/*",
                "Condition": {"StringNotEquals": {"aws:UserAgent": "AWS Redshift/Spectrum"}}
    }]
}
```

## Policies to grant minimum permissions<a name="spectrum-iam-policies-minimum-permissions"></a>

The following policy grants the minimum permissions required to use Redshift Spectrum with Amazon S3, AWS Glue, and Athena\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListMultipartUploadParts",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/folder1/folder2/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:UpdateDatabase",
                "glue:CreateTable",
                "glue:DeleteTable",
                "glue:BatchDeleteTable",
                "glue:UpdateTable",
                "glue:GetTable",
                "glue:GetTables",
                "glue:BatchCreatePartition",
                "glue:CreatePartition",
                "glue:DeletePartition",
                "glue:BatchDeletePartition",
                "glue:UpdatePartition",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:BatchGetPartition"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

If you use Athena for your data catalog instead of AWS Glue, the policy requires full Athena access\. The following policy grants access to Athena resources\. If your external database is in a Hive metastore, you don't need Athena access\.

```
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": ["athena:*"],
		"Resource": ["*"]
	}]
}
```

## Chaining IAM roles in Amazon Redshift Spectrum<a name="c-spectrum-chaining-roles"></a>

When you attach a role to your cluster, your cluster can assume that role to access Amazon S3, Athena, and AWS Glue on your behalf\. If a role attached to your cluster doesn't have access to the necessary resources, you can chain another role, possibly belonging to another account\. Your cluster then temporarily assumes the chained role to access the data\. You can also grant cross\-account access by chaining roles\. You can chain a maximum of 10 roles\. Each role in the chain assumes the next role in the chain, until the cluster assumes the role at the end of chain\. 

To chain roles, you establish a trust relationship between the roles\. A role that assumes another role must have a permissions policy that allows it to assume the specified role\. In turn, the role that passes permissions must have a trust policy that allows it to pass its permissions to another role\. For more information, see [Chaining IAM Roles in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html#authorizing-redshift-service-chaining-roles)\. 

When you run the CREATE EXTERNAL SCHEMA command, you can chain roles by including a comma\-separated list of role ARNs\. 

**Note**  
The list of chained roles must not include spaces\.

In the following example, `MyRedshiftRole` is attached to the cluster\. `MyRedshiftRole` assumes the role `AcmeData`, which belongs to account `111122223333`\.

```
create external schema acme from data catalog 
database 'acmedb' region 'us-west-2' 
iam_role 'arn:aws:iam::123456789012:role/MyRedshiftRole,arn:aws:iam::111122223333:role/AcmeData';
```

## Controlling access to the AWS Glue Data Catalog<a name="c-spectrum-glue-acess"></a>

If you use AWS Glue for your data catalog, you can apply fine\-grained access control to the AWS Glue Data Catalog with your IAM policy\. For example, you might want to expose only a few databases and tables to a specific IAM role\.

The following sections describe the IAM policies for various levels of access to data stored in the AWS Glue Data Catalog\.

**Topics**
+ [Policy for database operations](#c-spectrum-glue-acess-database)
+ [Policy for table operations](#c-spectrum-glue-acess-tables)
+ [Policy for partition operations](#c-spectrum-glue-acess-partitions)

### Policy for database operations<a name="c-spectrum-glue-acess-database"></a>

If you want to give users permissions to view and create a database, they need access rights to both the database and the AWS Glue Data Catalog\.

The following example query creates a database\.

```
CREATE EXTERNAL SCHEMA example_db
FROM DATA CATALOG DATABASE 'example_db' region 'us-west-2' 
IAM_ROLE 'arn:aws:iam::redshift-account:role/spectrumrole'
CREATE EXTERNAL DATABASE IF NOT EXISTS
```

The following IAM policy gives the minimum permissions required for creating a database\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetDatabase",
                "glue:CreateDatabase"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:catalog"
            ]
        }
    ]
}
```

The following example query lists the current databases\.

```
SELECT * FROM SVV_EXTERNAL_DATABASES WHERE
databasename = 'example_db1' or databasename = 'example_db2';
```

The following IAM policy gives the minimum permissions required to list the current databases\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetDatabases"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:database/example_db1",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db2",
                "arn:aws:glue:us-west-2:redshift-account:catalog"

            ]
        }
    ]
}
```

### Policy for table operations<a name="c-spectrum-glue-acess-tables"></a>

If you want to give users permissions to view, create, drop, alter, or take other actions on tables, they need several types of access\. They need access to the tables themselves, the databases they belong to, and the catalog\.

The following example query creates an external table\.

```
CREATE EXTERNAL TABLE example_db.example_tbl0(
    col0 INT,
    col1 VARCHAR(255)
) PARTITIONED BY (part INT) STORED AS TEXTFILE
LOCATION 's3://test/s3/location/';
```

The following IAM policy gives the minimum permissions required to create an external table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateTable"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```

The following example queries each list the current external tables\.

```
SELECT * FROM svv_external_tables
WHERE tablename = 'example_tbl0' OR
tablename = 'example_tbl1';
```

```
SELECT * FROM svv_external_columns
WHERE tablename = 'example_tbl0' OR
tablename = 'example_tbl1';
```

```
SELECT parameters FROM svv_external_tables
WHERE tablename = 'example_tbl0' OR
tablename = 'example_tbl1';
```

The following IAM policy gives the minimum permissions required to list the current external tables\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetTables"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl1"

            ]
        }
    ]
}
```

The following example query alters an existing table\.

```
ALTER TABLE example_db.example_tbl0
SET TABLE PROPERTIES ('numRows' = '100');
```

The following IAM policy gives the minimum permissions required to alter an existing table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetTable",
                "glue:UpdateTable"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"

            ]
        }
    ]
}
```

The following example query drops an existing table\.

```
DROP TABLE example_db.example_tbl0;                       
```

The following IAM policy gives the minimum permissions required to drop an existing table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:DeleteTable"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```

### Policy for partition operations<a name="c-spectrum-glue-acess-partitions"></a>

If you want to give users permissions to perform partition\-level operations \(view, create, drop, alter, and so on\), they need permissions to the tables that the partitions belong to\. They also need permissions to the related databases and the AWS Glue Data Catalog\.

The following example query creates a partition\.

```
ALTER TABLE example_db.example_tbl0
ADD PARTITION (part=0) LOCATION 's3://test/s3/location/part=0/';
ALTER TABLE example_db.example_t
ADD PARTITION (part=1) LOCATION 's3://test/s3/location/part=1/';
```

The following IAM policy gives the minimum permissions required to create a partition\.

```
 {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetTable",
                "glue:BatchCreatePartition"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```

The following example query lists the current partitions\.

```
SELECT * FROM svv_external_partitions
WHERE schemname = 'example_db' AND
tablename = 'example_tbl0'
```

The following IAM policy gives the minimum permissions required to list the current partitions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetPartitions",
                "glue:GetTables",
                "glue:GetTable"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```

The following example query alters an existing partition\.

```
ALTER TABLE example_db.example_tbl0 PARTITION(part='0')
SET LOCATION 's3://test/s3/new/location/part=0/';
```

The following IAM policy gives the minimum permissions required to alter an existing partition\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:GetPartition",
                "glue:UpdatePartition"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```

The following example query drops an existing partition\.

```
ALTER TABLE example_db.example_tbl0 DROP PARTITION(part='0');               
```

The following IAM policy gives the minimum permissions required to drop an existing partition\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:DeletePartition"
            ],
            "Resource": [
                "arn:aws:glue:us-west-2:redshift-account:catalog",
                "arn:aws:glue:us-west-2:redshift-account:database/example_db",
                "arn:aws:glue:us-west-2:redshift-account:table/example_db/example_tbl0"
            ]
        }
    ]
}
```