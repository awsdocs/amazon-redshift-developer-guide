# IAM Policies for Amazon Redshift Spectrum<a name="c-spectrum-iam-policies"></a>

By default, Amazon Redshift Spectrum uses the Amazon Athena data catalog\. Your cluster needs authorization to access your external data catalog in AWS Glue or Athena and your data files in Amazon S3\. You provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster\. If you use an Apache Hive metastore to manage your data catalog, you don't need to provide access to Athena\.

**Note**  
If you currently have Redshift Spectrum external tables in the Athena data catalog, you can migrate your Athena data catalog to an AWS Glue Data Catalog\. To use the AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](http://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Athena User Guide*\.

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

The following policy grants access to Amazon Athena resources\. If your external database is in a Hive metastore, you don't need Amazon Athena access\.

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