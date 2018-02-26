# IAM Policies for Amazon Redshift Spectrum<a name="c-spectrum-iam-policies"></a>

<<<<<<< HEAD
By default, Amazon Redshift Spectrum uses the AWS Glue data catalog in regions that support AWS Glue\. In other regions, Redshift Spectrum uses the Athena data catalog\. Your cluster needs authorization to access your external data catalog in AWS Glue or Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. If you use an Apache Hive metastore to manage your data catalog, you don't need to provide access to Athena\.

You can chain roles so that your cluster can assume other roles not attached to the cluster\. For more information, see [Chaining IAM Roles in Amazon Redshift Spectrum](#c-spectrum-chaining-roles)\.


+ [Amazon S3 Permissions](#spectrum-iam-policies-s3)
+ [Cross\-Account Amazon S3 Permissions](#spectrum-iam-policies-cross-account)
+ [Policies to Grant or Restrict Redshift Spectrum Access](#spectrum-iam-policies-spectrum-only)
+ [Policies to Grant Minimum Permissions](#spectrum-iam-policies-minimum-permissions)
+ [Chaining IAM Roles in Amazon Redshift Spectrum](#c-spectrum-chaining-roles)

**Note**  
If you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see http://docs\.aws\.amazon\.com/athena/latest/ug/[Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

## Amazon S3 Permissions<a name="spectrum-iam-policies-s3"></a>
=======
By default, Amazon Redshift Spectrum uses the Amazon Athena data catalog\. Your cluster needs authorization to access your external data catalog in AWS Glue or Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. If you use an Apache Hive metastore to manage your data catalog, you don't need to provide access to Athena\.

**Note**  
If you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d

At a minimum, your cluster needs GET and LIST access to your Amazon S3 bucket\. If your bucket is not in the same AWS account as your cluster, your bucket must also authorize your cluster to access the data\. For more information, see [ Authorizing Amazon Redshift to Access Other AWS Services on Your Behalf](http://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html)\. 

**Note**  
The Amazon S3 bucket can't use a bucket policy that restricts access only from specific VPC endpoints\.

The following policy grants GET and LIST access to any Amazon S3 bucket\. The policy allows access to Amazon S3 buckets for Redshift Spectrum as well as COPY and UNLOAD operations\.

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
[{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": ["s3:Get*", "s3:List*"],
		"Resource": "arn:aws:s3:::myBucket/*"
	}]
}
```

<<<<<<< HEAD
## Cross\-Account Amazon S3 Permissions<a name="spectrum-iam-policies-cross-account"></a>

To grant Redshift Spectrum permission to access data in an Amazon S3 bucket that belongs to another AWS account, add the following policy to the Amazon S3 bucket\. For more information, see [Granting Cross\-Account Bucket Permissions](http://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access-example2.html)\.

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

## Policies to Grant or Restrict Redshift Spectrum Access<a name="spectrum-iam-policies-spectrum-only"></a>

=======
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d
To grant access to an Amazon S3 bucket only using Redshift Spectrum, include a condition that allows access for the user agent `AWS Redshift/Spectrum`\. The following policy allows access to Amazon S3 buckets only for Redshift Spectrum\. It excludes other access, such as COPY and UNLOAD operations\.

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

Similarly, you might want to create an IAM role that allows access for COPY and UNLOAD operations, but excludes Redshift Spectrum access\. To do so, include a condition that denies access for the user agent "AWS Redshift/Spectrum"\. The following policy allows access to an Amazon S3 bucket with the exception of Redshift Spectrum\.

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

<<<<<<< HEAD
## Policies to Grant Minimum Permissions<a name="spectrum-iam-policies-minimum-permissions"></a>

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
```

If you use Athena for your data catalog instead of AWS Glue, the policy requires full Athena access\. The following policy grants access to Athena resources\. If your external database is in a Hive metastore, you don't need Athena access\.
=======
The following policy grants access to Amazon Athena resources\. If your external database is in a Hive metastore, you don't need Amazon Athena access\.
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d

```
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": ["athena:*"],
		"Resource": ["*"]
	}]
}
<<<<<<< HEAD
```

## Chaining IAM Roles in Amazon Redshift Spectrum<a name="c-spectrum-chaining-roles"></a>

When you attach a role to your cluster, your cluster can assume that role to access Amazon S3, Athena, and AWS Glue on your behalf\. If a role attached to your cluster doesn't have access to the necessary resources, you can chain another role, possibly belonging to another account\. Your cluster then temporarily assumes the chained role to access the data\. You can also grant cross\-account access by chaining roles\. You can chain a maximum of 10 roles\. Each role in the chain assumes the next role in the chain, until the cluster assumes the role at the end of chain\. 

To chain roles, you establish a trust relationship between the roles\. A role that assumes another role must have a permissions policy that allows it to assume the specified role\. In turn, the role that passes permissions must have a trust policy that allows it to pass its permissions to another role\. For more information, see [Chaining IAM Roles in Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html#authorizing-redshift-service-chaining-roles)\. 

When you run the CREATE EXTERNAL SCHEMA command, you can chain roles by including a comma separated list of role ARNs\. 

**Note**  
The list of chained roles must not include spaces\.

In the following example, `MyRedshiftRole` is attached to the cluster\. `MyRedshiftRole` assumes the role `AcmeData`, which belongs to account `210987654321`\.

```
create external schema acme from data catalog 
database 'acmedb' region 'us-west-2' 
iam_role 'arn:aws:iam::123456789012:role/MyRedshiftRole,arn:aws:iam::210987654321:role/AcmeData';
=======
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d
```